-- Script em ServerScriptService
local Players = game:GetService("Players")
local PhysicsService = game:GetService("PhysicsService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

-- IDs que podem usar o No-Hitbox
local ADMIN_IDS = {12345678, 23456789} -- troque para os UserIds autorizados
local GROUP = "NoHit"

-- Cria RemoteEvent
local remote = Instance.new("RemoteEvent")
remote.Name = "ToggleNoHitbox"
remote.Parent = ReplicatedStorage

-- Configuração do grupo de colisão
pcall(function() PhysicsService:RegisterCollisionGroup(GROUP) end)
PhysicsService:CollisionGroupSetCollidable(GROUP, "Default", false)
PhysicsService:CollisionGroupSetCollidable(GROUP, GROUP, false)

local function setCharacterGroup(character, groupName)
	for _, obj in ipairs(character:GetDescendants()) do
		if obj:IsA("BasePart") then
			PhysicsService:SetPartCollisionGroup(obj, groupName)
			obj.CanTouch = (groupName ~= GROUP)
		end
	end
end

local function applyNoHitbox(player, enabled)
	player:SetAttribute("NoHitbox", enabled)
	local character = player.Character
	if character then
		if enabled then
			setCharacterGroup(character, GROUP)
		else
			setCharacterGroup(character, "Default")
		end
	end
end

Players.PlayerAdded:Connect(function(player)
	player.CharacterAdded:Connect(function(char)
		if player:GetAttribute("NoHitbox") then
			setCharacterGroup(char, GROUP)
		end
	end)
end)

-- Quando o botão é clicado
remote.OnServerEvent:Connect(function(player, enabled)
	if not table.find(ADMIN_IDS, player.UserId) then return end
	applyNoHitbox(player, enabled)
end)# Dkdhdhsh
