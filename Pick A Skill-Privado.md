local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()
local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/SaveManager.lua"))()
local InterfaceManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/InterfaceManager.lua"))()

local Window = Fluent:CreateWindow({
    Title = "Ari´s HUB ",
    SubTitle = "by Ari",
    TabWidth = 160,
    Size = UDim2.fromOffset(580, 460),
    Acrylic = true,
    Theme = "Dark",
    MinimizeKey = Enum.KeyCode.LeftControl
})

local Tabs = {
    Main = Window:AddTab({ Title = "Home", Icon = "" }),
    Kill = Window:AddTab({ Title = "Kill", Icon = "" }),
    Gun = Window:AddTab({ Title = "Guns", Icon = "" })
}

local Options = Fluent.Options

Fluent:Notify({
    Title = "ARI´S HUB ON TOP!!",
    Content = "Loaded!",
    Duration = 3
})

Tabs.Main:AddParagraph({
    Title = "Private Script",
    Content = "Private by ARI.\n"
})

local Toggle = Tabs.Kill:AddToggle("MyToggle", { Title = "Kill Server", Default = false })

-- Script de ataque
local attacking = false

Toggle:OnChanged(function()
    attacking = Options.MyToggle.Value

    if attacking then
        task.spawn(function()
            while attacking do
                local repeats = 100
                local delay = 0.2

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

                for i = 1, repeats do
                    if not attacking then break end
                    for _, player in ipairs(Players:GetPlayers()) do
                        if not attacking then break end

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

                            pcall(function()
                                DamageEvent:FireServer(unpack(args))
                            end)

                            task.wait(delay)
                        end
                    end
                end

                task.wait()
            end
        end)
    end
end)

Options.MyToggle:SetValue(false)

-- Botão com delay de 8 segundos por arma
Tabs.Gun:AddButton({
    Title = "Get all secrets weapons",
    Description = "Desbloqueia uma a uma com delay",
    Callback = function()
        Window:Dialog({
            Title = "Get all secret itens?",
            Content = "Ok... click 'Confirm'",
            Buttons = {
                {
                    Title = "Confirm",
                    Callback = function()
                        local ReplicatedStorage = game:GetService("ReplicatedStorage")
                        local Remote = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("UnlockWeapon")

                        local weapons = {
                            "Purple Void",
                            "Asta",
                            "Piano",
                            "Rasengan",
                            "Bloodmoon",
                            "Dark Blade"
                        }

                        task.spawn(function()
                            for _, weapon in ipairs(weapons) do
                                Remote:FireServer(weapon)
                                Fluent:Notify({
                                    Title = "Unlocked",
                                    Content = weapon,
                                    Duration = 2
                                })
                                task.wait(6)
                            end
                            Fluent:Notify({
                                Title = "Finalizado!",
                                Content = "Todas as armas secretas foram desbloqueadas.",
                                Duration = 4
                            })
                        end)
                    end
                },
                {
                    Title = "Cancel",
                    Callback = function()
                        print("Cancelled the dialog.")
                    end
                }
            }
        })
    end
})
