-- Variável para controlar o estado do script
local scriptAtivo = true

-- Função para alternar o estado do script
function toggleScript()
    scriptAtivo = not scriptAtivo
    if scriptAtivo then
        print("Script ativado")
    else
        print("Script desativado")
    end
end

-- Função para garantir que o HUD permaneça visível
function ativarHUD()
    game.StarterGui:SetCoreGuiEnabled(Enum.CoreGuiType.All, true)
end

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

-- Garantir que o HUD permaneça visível
game.StarterGui:SetCoreGuiEnabled(Enum.CoreGuiType.All, true)

-- Wait for 2 seconds before executing the Autofarm script
task.wait(2)

-- Autofarm Script
local Workspace = game:GetService("Workspace")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local LocalPlayer = Players.LocalPlayer
local Character = LocalPlayer.Character
local HumanoidRootPart = Character.HumanoidRootPart
local States = LocalPlayer:FindFirstChild("States")
local Stats = LocalPlayer:FindFirstChild("Stats")

local RobRemote = ReplicatedStorage:FindFirstChild("GeneralEvents"):FindFirstChild("Rob")
local BagLevel = Stats:FindFirstChild("BagSizeLevel"):FindFirstChild("CurrentAmount")
local BagAmount = States:FindFirstChild("Bag")

local Camp = CFrame.new(1636.62537, 104.349976, -1736.184)

local function TeleportToCamp()
    HumanoidRootPart.CFrame = Camp
end

local function CashRegisterFarm()
    for _, Item in ipairs(Workspace:GetChildren()) do
        if BagAmount.Value == BagLevel.Value then
            TeleportToCamp()
            break
        elseif Item:IsA("Model") and Item.Name == "CashRegister" then
            local OpenPart = Item:FindFirstChild("Open")
            if OpenPart então
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
        if BagAmount.Value == BagLevel.Value então
            TeleportToCamp()
            break
        elseif Item:IsA("Model") e Item.Name == "Safe" e Item:FindFirstChild("Amount").Value > 0 então
            local SafePart = Item:FindFirstChild("Safe")
            if SafePart então
                HumanoidRootPart.CFrame = SafePart.CFrame
                if Item:FindFirstChild("Open").Value então
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
    if scriptAtivo então
        CashRegisterFarm()
        BankFarm()
        ativarHUD() -- Garantir que o HUD permaneça visível a cada frame
    end
end)

-- Alterna o estado do script ao pressionar a tecla F
local UserInputService = game:GetService("UserInputService")
UserInputService.InputBegan:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.F então
        toggleScript()
    end
end)
