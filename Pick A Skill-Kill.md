-- Matar o server, COLOQUE false PARA PARAR!
_G.auto = true
while _G.auto do
    
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local DamageEvent = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("DamagePlayer")

local function getHitSound()
    local bat = LocalPlayer.Character:FindFirstChild("Bat")
    if bat then
        local data = bat:FindFirstChild("WeaponData")
        if data then
            return data:FindFirstChild("Hit")
        end
    end
    return nil
end

for _, player in ipairs(Players:GetPlayers()) do
    if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Humanoid") then
        local args = {
            [1] = {},
            [2] = player,
            [3] = player.Character.Humanoid,
            [4] = 20,
            [5] = {
                ["AttackSpeed"] = 0,
                ["HitSound"] = getHitSound()
            }
        }

        local success, err = pcall(function()
            DamageEvent:FireServer(unpack(args))
        end)

        if not success then
            warn("Falha ao tentar atingir " .. player.Name .. ": " .. err)
        end

        task.wait(0.2)
    end
end

wait()

end
