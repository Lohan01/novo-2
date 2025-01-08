-- GODMODE Script
game.StarterGui:SetCore("SendNotification", {
    Title = "Godmode";
    Text = "Godmode executed";
    Duration = 5;
})

local speaker = game.Players.LocalPlayer
local Cam = workspace.CurrentCamera
local Pos, Char = Cam.CFrame, speaker.Character
local Human = Char:FindFirstChildWhichIsA("Humanoid")

if Human then
    local nHuman = Human:Clone()
    nHuman.Parent = Char
    speaker.Character = nil
    nHuman:SetStateEnabled(Enum.HumanoidStateType.Dead, false)
    nHuman:SetStateEnabled(Enum.HumanoidStateType.FallingDown, false)
    nHuman:SetStateEnabled(Enum.HumanoidStateType.Physics, false)
    Human:Destroy()
    speaker.Character = Char
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

local LocalPlayer = Players.LocalPlayer
local Character = LocalPlayer.Character
local HumanoidRootPart = Character:FindFirstChild("HumanoidRootPart")
local States = LocalPlayer:FindFirstChild("States")
local Stats = LocalPlayer:FindFirstChild("Stats")

local RobRemote = ReplicatedStorage:FindFirstChild("GeneralEvents"):FindFirstChild("Rob")
local BagLevel = Stats:FindFirstChild("BagSizeLevel"):FindFirstChild("CurrentAmount")
local BagAmount = States:FindFirstChild("Bag")

local Camp = CFrame.new(1636.62537, 104.349976, -1736.184)

local function TeleportToCamp()
    if HumanoidRootPart then
        HumanoidRootPart.CFrame = Camp
    end
end

local function CashRegisterFarm()
    for _, Item in ipairs(Workspace:GetChildren()) do
        if BagAmount.Value == BagLevel.Value then
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
            end
        end
    end
end

local function BankFarm()
    for _, Item in ipairs(Workspace:GetChildren()) do
        if BagAmount.Value == BagLevel.Value then
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
            end
        end
    end
end

-- Main Execution
RunService.RenderStepped:Connect(function()
    if HumanoidRootPart then
        CashRegisterFarm()
        BankFarm()
    end
end)
