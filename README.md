-- Coloque em StarterPlayerScripts como LocalScript

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local mouse = player:GetMouse()
local character = player.Character or player.CharacterAdded:Wait()
local hrp = character:WaitForChild("HumanoidRootPart")

-- GUI construction
local gui = Instance.new("ScreenGui", player.PlayerGui)
gui.Name = "YummiHubGUI"

-- Main Frame
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 300, 0, 180)
frame.Position = UDim2.new(0.5, -150, 0.5, -90)
frame.BackgroundColor3 = Color3.fromRGB(25, 0, 40)
frame.BorderSizePixel = 0
frame.Visible = false
frame.Parent = gui

-- Title
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 40)
title.BackgroundColor3 = Color3.fromRGB(45, 0, 90)
title.Text = "Yummi Hub"
title.TextColor3 = Color3.fromRGB(183, 106, 255)
title.Font = Enum.Font.GothamBold
title.TextSize = 32
title.Parent = frame

-- AutoRaid Toggle
local autoRaid = false
local button = Instance.new("TextButton")
button.Size = UDim2.new(0.8, 0, 0, 40)
button.Position = UDim2.new(0.1, 0, 0.4, 0)
button.BackgroundColor3 = Color3.fromRGB(30, 0, 80)
button.Text = "Auto Raid [OFF]"
button.TextColor3 = Color3.fromRGB(244, 200, 255)
button.Font = Enum.Font.GothamBlack
button.TextSize = 22
button.Parent = frame

-- Menu Open/Close Hotkey (RightShift)
UIS.InputBegan:Connect(function(input, gp)
    if gp then return end
    if input.KeyCode == Enum.KeyCode.RightShift then
        frame.Visible = not frame.Visible
    end
end)

-- Helper: Teleport
local function tpTo(part)
    if part then
        hrp.CFrame = part.CFrame + Vector3.new(0,0,3)
    end
end

-- Helper: Talk to NPC
local function talkToBleachNPC()
    local bleachNPC = workspace:FindFirstChild("Bleach Raid")
    if bleachNPC and bleachNPC:FindFirstChild("HumanoidRootPart") then
        tpTo(bleachNPC.HumanoidRootPart)
        wait(0.6)
        -- Procurar e ativar ProximityPrompt (nativo/exploit)
        for _, v in ipairs(bleachNPC:GetDescendants()) do
            if v:IsA("ProximityPrompt") then
                if fireproximityprompt then
                    fireproximityprompt(v)
                else
                    v:InputHoldBegin()
                    wait(0.15)
                    v:InputHoldEnd()
                end
                break
            end
        end
    end
end

-- Helper: Selecionar opção no diálogo
local function selectDialogueOption()
    -- Tenta escolher a opção 1 (pode ser que precise adaptar para seu jogo)
    -- Exemplo genérico para clicar/ativar
    for i, descendant in ipairs(game.CoreGui:GetDescendants()) do
        if descendant:IsA("TextButton") and descendant.Text == "I want to fight the villains!" then
            descendant:Activate()
            return
        end
    end
    -- Alternativa: simular tecla "1"
    UIS.InputBegan:Fire({KeyCode = Enum.KeyCode.One, UserInputType = Enum.UserInputType.Keyboard}, false)
end

-- Helper: Matar o inimigo mais próximo
local function killNearestEnemy()
    while autoRaid do
        local nearest, nearestDist
        for _, npc in ipairs(workspace:GetDescendants()) do
            if npc:IsA("Model") and npc ~= player.Character and npc:FindFirstChild("Humanoid") and npc:FindFirstChild("HumanoidRootPart") then
                if npc.Humanoid.Health > 0 and npc:FindFirstChild("IsEnemy") then -- adaptar para checar se é inimigo
                    local dist = (hrp.Position - npc.HumanoidRootPart.Position).Magnitude
                    if not nearestDist or dist < nearestDist then
                        nearest = npc
                        nearestDist = dist
                    end
                end
            end
        end
        if nearest and nearest:FindFirstChild("Humanoid") then
            -- Simula ataque: adapta conforme o jogo
            nearest.Humanoid.Health = 0 -- ou use o remote de ataque do jogo
        end
        wait(0.6)
    end
end

button.MouseButton1Click:Connect(function()
    autoRaid = not autoRaid
    button.Text = autoRaid and "Auto Raid [ON]" or "Auto Raid [OFF]"
    button.BackgroundColor3 = autoRaid and Color3.fromRGB(90, 0, 120) or Color3.fromRGB(30, 0, 80)
    if autoRaid then
        -- Fala com NPC e começa a raid
        talkToBleachNPC()
        wait(1)
        selectDialogueOption()
        wait(1.5)
        -- Começa o loop de kill nearest
        spawn(function()
            killNearestEnemy()
        end)
    end
end)

-- Arrastar o menu (opcional)
local dragging, dragInput, dragStart, startPos
title.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = frame.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

title.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        dragInput = input
    end
end)

UIS.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        local delta = input.Position - dragStart
        frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)
