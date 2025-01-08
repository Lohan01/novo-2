-- Invincibility Script

local speaker = game.Players.LocalPlayer

local function tornarInvencivel()
    local Char = speaker.Character
    if Char then
        local Human = Char:FindFirstChildWhichIsA("Humanoid")
        if Human then
            Human:GetPropertyChangedSignal("Health"):Connect(function()
                if Human.Health < Human.MaxHealth then
                    Human.Health = Human.MaxHealth
                end
            end)
            Human.Health = Human.MaxHealth
        end
    end
end

speaker.CharacterAdded:Connect(function()
    wait(1)
    tornarInvencivel()
end)

if speaker.Character then
    tornarInvencivel()
end
