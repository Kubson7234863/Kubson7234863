local players = game:GetService("Players")
local dataStore = game:GetService("DataStoreService")

local plrDataStore = dataStore:GetDataStore("PlayerDataStore")

local function save(player)
	local money = player.leaderstats.Money.Value

	local success, errorMsg = pcall(function()
		plrDataStore:SetAsync(player.UserId, money)
	end)

	if not success then
		warn(errorMsg)
	end
end

local function saveAll()
	local plrs = players:GetPlayers()
	for _, plr in pairs(plrs) do
		save(plr)
	end
end

local function load(player)
	local leaderstats = Instance.new("Folder")
	leaderstats.Parent = player
	leaderstats.Name = 'leaderstats'

	local money = Instance.new("IntValue")
	money.Parent = player.leaderstats
	money.Name = 'Money'

	local success, errorMsg = pcall(function()
		local data = plrDataStore:GetAsync(player.UserId)
		if data then
			money.Value = data
		else
			money.Value = 0		
		end
	end)

	if not success then
		player:Kick("Wystąpił Nieoczekiwany Problem Z Wczytaniem Twoich Dan Robloxowych")
		warn(errorMsg)
	end
end

players.PlayerAdded:Connect(load)
players.PlayerRemoving:Connect(save)
game:BindToClose(saveAll)
