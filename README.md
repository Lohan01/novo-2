-- GODMODE Script
game.StarterGui:SetCore("SendNotification", {
    Title = "Godmode";
    Text = "Godmode executed";
    Duration = 5;
})

local player = game.Players.LocalPlayer
local Cam = workspace.CurrentCamera
local Pos, Char = Cam.CFrame, player.Character
local Human = Char:FindFirstChildWhichIsA("Humanoid")

if Human then
    local nHuman = Human:Clone()
    nHuman.Parent = Char
    player.Character = nil
    nHuman:SetStateEnabled(Enum.HumanoidStateType.Dead, false)
    nHuman:SetStateEnabled(Enum.HumanoidStateType.FallingDown, false)
    nHuman:SetStateEnabled(Enum.HumanoidStateType.Physics, false)
    Human:Destroy()
    player.Character = Char
    Cam.CameraSubject = nHuman
    Cam.CFrame = Pos
    nHuman.DisplayDistanceType = Enum.HumanoidDisplayDistanceType.None

    local AnimateScript = Char:FindFirstChild("Animate")
    if AnimateScript then
        AnimateScript.Disabled = true
        wait()
        AnimateScript.Disabled = false
    end

    nHuman.Health = nHuman.MaxHealth
end

-- Wait for 2 seconds before executing the Autofarm script
task.wait(2)

-- Autofarm Script
local Workspace = game:GetService("Workspace")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local LocalPlayer = Players.LocalPlayer
local Character = LocalPlayer.Character
local HumanoidRootPart = Character:FindFirstChild("HumanoidRootPart")
local States = LocalPlayer:FindFirstChild("States")
local Stats = LocalPlayer:FindFirstChild("Stats")

local RobRemote = ReplicatedStorage:FindFirstChild("GeneralEvents"):FindFirstChild("Rob")
local BagLevel = Stats:FindFirstChild("BagSizeLevel"):FindFirstChild("CurrentAmount")
local BagAmount = States:FindFirstChild("Bag")

local Camp = CFrame.new(1636.62537, 104.349976, -1736.184)

local autoFarmEnabled = true -- Variable to control the Auto Farm script

local function TeleportToCamp()
    HumanoidRootPart.CFrame = Camp
end

local function CashRegisterFarm()
    if not autoFarmEnabled then return end
    for _, Item in ipairs(Workspace:GetChildren()) do
        if BagAmount.Value >= BagLevel.Value then
            TeleportToCamp()
            break
        elseif Item:IsA("Model") and Item.Name == "CashRegister" then
            local OpenPart = Item:FindFirstChild("Open")
            if OpenPart then
                HumanoidRootPart.CFrame = OpenPart.CFrame
                RobRemote:FireServer("Register", {
                    ["Part"] = Item:FindFirstChild("Union"),
                    ["OpenPart"] = OpenPart,
                    ["ActiveValue"] = Item:FindFirstChild("Active"),
                    ["Active"] = true
                })
                wait(1)
            end
        end
    end
end

local function BankFarm()
    if not autoFarmEnabled then return end
    for _, Item in ipairs(Workspace:GetChildren()) do
        if BagAmount.Value >= BagLevel.Value then
            TeleportToCamp()
            break
        elseif Item:IsA("Model") and Item.Name == "Safe" and Item:FindFirstChild("Amount").Value > 0 then
            local SafePart = Item:FindFirstChild("Safe")
            if SafePart then
                HumanoidRootPart.CFrame = SafePart.CFrame
                if Item:FindFirstChild("Open").Value then
                    RobRemote:FireServer("Safe", Item)
                else
                    Item:FindFirstChild("OpenSafe"):FireServer("Completed")
                    RobRemote:FireServer("Safe", Item)
                end
                wait(1)
            end
        end
    end
end

local function autoFarm()
    while true do
        if autoFarmEnabled then
            CashRegisterFarm()
            BankFarm()
        end
        wait(5)
    end
end

-- Toggle Auto Farm on and off using the "P" key
UserInputService.InputBegan:Connect(function(input, gameProcessedEvent)
    if input.KeyCode == Enum.KeyCode.P and not gameProcessedEvent then
        autoFarmEnabled = not autoFarmEnabled
        game.StarterGui:SetCore("SendNotification", {
            Title = "Auto Farm";
            Text = autoFarmEnabled and "Enabled" or "Disabled";
            Duration = 5;
        })
    end
end)

-- Main Execution
RunService.RenderStepped:Connect(function()
    if autoFarmEnabled then
        CashRegisterFarm()
        BankFarm()
    end
end)

-- Start Auto Farm
spawn(autoFarm)
