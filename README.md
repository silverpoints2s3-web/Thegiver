local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local VirtualInputManager = game:GetService("VirtualInputManager")

local localPlayer = Players.LocalPlayer
local camera = workspace.CurrentCamera
local actif = false
local enCombo = false

-- --- RÉGLAGES ---
local distAttaque = 15
local distArret = 7      -- Distance pour arrêter de marcher et ne pas monter sur le NPC
local vitesseSuivi = 0.6
local dureeAttaque = 5.0 
local dureeDash = 2.0    

-- Nettoyage
for _, v in pairs(localPlayer.PlayerGui:GetChildren()) do
    if v.Name == "TennySystem" then v:Destroy() end
end

-- --- INTERFACE ---
local screenGui = Instance.new("ScreenGui", localPlayer.PlayerGui)
screenGui.Name = "TennySystem"
local frame = Instance.new("Frame", screenGui)
frame.Size = UDim2.new(0, 220, 0, 70)
frame.Position = UDim2.new(0.5, -110, 0.1, 0)
frame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
frame.BorderSizePixel = 2
frame.BorderColor3 = Color3.fromRGB(130, 0, 0)

local label = Instance.new("TextLabel", frame)
label.Size = UDim2.new(1, 0, 1, 0)
label.BackgroundTransparency = 1
label.Text = "TSB FIXED: OFF"
label.Font = Enum.Font.GothamBold
label.TextColor3 = Color3.fromRGB(110, 110, 110)

-- --- SIMULATION ---
local function press(key)
    VirtualInputManager:SendKeyEvent(true, key, false, game)
    task.wait(0.05)
    VirtualInputManager:SendKeyEvent(false, key, false, game)
end

-- --- COMBO ---
local function executerCombo()
    if enCombo then return end
    enCombo = true
    VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.W, false, game)
    press(Enum.KeyCode.One)
    task.wait(dureeAttaque - 0.2) 
    press(Enum.KeyCode.Q)
    task.wait(dureeDash) 
    press(Enum.KeyCode.Two)
    task.wait(dureeAttaque)
    VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.W, false, game)
    enCombo = false
end

-- --- ACTIVATION ---
UserInputService.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
        local pos = input.Position
        local bPos = frame.AbsolutePosition
        local bSize = frame.AbsoluteSize
        if pos.X >= bPos.X and pos.X <= (bPos.X + bSize.X) and pos.Y >= bPos.Y and pos.Y <= (bPos.Y + bSize.Y) then
            actif = not actif
            label.Text = actif and "TSB FIXED: ON" or "TSB FIXED: OFF"
            label.TextColor3 = actif and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(110, 110, 110)
        end
    end
end)

-- --- BOUCLE PRINCIPALE ---
RunService.RenderStepped:Connect(function()
    if actif and localPlayer.Character and localPlayer.Character:FindFirstChild("Humanoid") then
        local myRoot = localPlayer.Character.PrimaryPart
        local hum = localPlayer.Character.Humanoid
        local cible = nil
        local distMin = 100
        
        for _, v in pairs(workspace:GetDescendants()) do
            if v:IsA("Humanoid") and v.Parent ~= localPlayer.Character and v.Health > 0 then
                local root = v.Parent:FindFirstChild("HumanoidRootPart")
                if root then
                    local d = (root.Position - myRoot.Position).Magnitude
                    if d < distMin then distMin = d cible = root end
                end
            end
        end
        
        if cible then
            camera.CFrame = camera.CFrame:Lerp(CFrame.new(camera.CFrame.Position, cible.Position), vitesseSuivi)
            
            -- CONDITION DE MOUVEMENT : On ne bouge que si on est trop loin
            if distMin > distArret then
                local direction = (cible.Position - myRoot.Position).Unit
                hum:Move(Vector3.new(direction.X, 0, direction.Z), true)
            else
                -- Si on est assez proche, on arrête de marcher
                hum:Move(Vector3.new(0, 0, 0), true)
            end
            
            if distMin <= distAttaque and not enCombo then
                task.spawn(executerCombo)
            end
        end
    end
end)
