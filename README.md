-- LocalScript (StarterPlayerScripts)
local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local hrp = character:WaitForChild("HumanoidRootPart")

-- UI Setup
local screengui = Instance.new("ScreenGui", player.PlayerGui)
local frame = Instance.new("Frame", screengui)
frame.Size = UDim2.new(0, 120, 0, 60)
frame.Position = UDim2.new(0, 10, 0, 10)
frame.BackgroundColor3 = Color3.fromRGB(25,25,32)

local button = Instance.new("TextButton", frame)
button.Size = UDim2.new(1, 0, 1, 0)
button.Text = "ON"
button.BackgroundColor3 = Color3.fromRGB(0,150,0)
button.TextColor3 = Color3.fromRGB(255,255,255)
button.Font = Enum.Font.SourceSansBold
button.TextSize = 22

local enabled = false
local debounce = false

button.MouseButton1Click:Connect(function()
    enabled = not enabled
    button.Text = enabled and "ON" or "OFF"
    button.BackgroundColor3 = enabled and Color3.fromRGB(0,150,0) or Color3.fromRGB(150,0,0)
    if enabled and not debounce then
        debounce = true

        -- 1. TELEPORTAR ATÉ O NPC "Bleach Raid"
        local bleedNpc = workspace:FindFirstChild("Bleach Raid")
        if bleedNpc and bleedNpc:FindFirstChild("HumanoidRootPart") then
            hrp.CFrame = bleedNpc.HumanoidRootPart.CFrame + Vector3.new(0, 0, 3)
            wait(0.5)
        end

        -- 2. SIMULAR TECLA "E" PARA INICIAR DIÁLOGO
        local function firePrompt(proximityPrompt)
            if proximityPrompt and proximityPrompt:IsA("ProximityPrompt") then
                fireproximityprompt = fireproximityprompt or getgenv().fireproximityprompt
                if fireproximityprompt then
                    fireproximityprompt(proximityPrompt)
                else
                    proximityPrompt:InputHoldBegin()
                    wait(0.1)
                    proximityPrompt:InputHoldEnd()
                end
            end
        end

        -- Procurar ProximityPrompt e ativar
        if bleedNpc then
            for _, v in ipairs(bleedNpc:GetDescendants()) do
                if v:IsA("ProximityPrompt") then
                    firePrompt(v)
                    break
                end
            end
        end

        wait(0.7)

        -- 3. SELECIONAR OPÇÃO "1" NO DIÁLOGO (pressionar tecla "1")
        UIS.InputBegan:Fire({KeyCode = Enum.KeyCode.One, UserInputType = Enum.UserInputType.Keyboard}, false)

        wait(1)

        -- 4. TELEPORTAR ATÉ O NPC "Grim"
        local grimNpc = workspace:FindFirstChild("Grim")
        if grimNpc and grimNpc:FindFirstChild("HumanoidRootPart") then
            hrp.CFrame = grimNpc.HumanoidRootPart.CFrame + Vector3.new(0,0,3)
        end

        wait(1)
        debounce = false
    end
end)
