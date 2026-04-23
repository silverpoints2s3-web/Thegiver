local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local VirtualInputManager = game:GetService("VirtualInputManager")

local localPlayer = Players.LocalPlayer
local camera = workspace.CurrentCamera
local actif = false
local enCombo = false

-- --- RÉGLAGES ---
local distAttaque = 12
local distArret = 3
local vitesseSuivi = 0.5

-- --- SIMULATION ---
local function press(key)
    VirtualInputManager:SendKeyEvent(true, key, false, game)
    task.wait(0.05)
    VirtualInputManager:SendKeyEvent(false, key, false, game)
end

-- --- LE KYOTO COMBO ---
local function executerKyotoCombo()
    if enCombo then return end
    enCombo = true
    
    -- 1. Approche et Saut d'ouverture
    press(Enum.KeyCode.Space)
    task.wait(0.2)
    
    -- 2. Série de coups normaux (M1) en l'air
    for i = 1, 3 do
        press(Enum.KeyCode.One) -- Ton bouton M1
        task.wait(0.4)
    end
    
    -- 3. Bousculade (Capacité 3) pour projeter
    press(Enum.KeyCode.Three)
    task.wait(0.6)
    
    -- 4. Dash de rattrapage (Q)
    press(Enum.KeyCode.Q)
    task.wait(0.2)
    
    -- 5. Coupe Supérieure (Capacité 4) pour finir le combo aérien
    press(Enum.KeyCode.Four)
    task.wait(1.5)
    
    enCombo = false
end

-- --- INTERFACE ---
local screenGui = Instance.new("ScreenGui", localPlayer.PlayerGui)
screenGui.Name = "KyotoSystem"
local frame = Instance.new("Frame", screenGui)
frame.Size = UDim2.new(0, 200, 0, 60)
frame.Position = UDim2.new(0.5, -100, 0.05, 0)
frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
local label = Instance.new("TextLabel", frame)
label.Size = UDim2.new(1, 0, 1, 0)
label.Text = "KYOTO MODE: OFF"
label.TextColor3 = Color3.fromRGB(255, 255, 255)

UserInputService.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
        local pos = input.Position
        local bPos = frame.AbsolutePosition
        local bSize = frame.AbsoluteSize
        if pos.X >= bPos.X and pos.X <= (bPos.X + bSize.X) and pos.Y >= bPos.Y and pos.Y <= (bPos.Y + bSize.Y) then
            actif = not actif
            label.Text = actif and "KYOTO MODE: ON" or "KYOTO MODE: OFF"
        end
    end
end)

-- --- BOUCLE DE DÉTECTION ---
RunService.RenderStepped:Connect(function()
    if actif and localPlayer.Character and localPlayer.Character:FindFirstChild("Humanoid") then
        local myRoot = localPlayer.Character.PrimaryPart
        local hum = localPlayer.Character.Humanoid
        local cible = nil
        local distMin = 50
        
        for _, v in pairs(workspace:GetDescendants()) do
            if v:IsA("Humanoid") and v.Parent ~= localPlayer.Character and v.Health > 0 then
                local root = v.Parent:FindFirstChild("HumanoidRootPart")
                if root and (root.Position - myRoot.Position).Magnitude < distMin then
                    distMin = (root.Position - myRoot.Position).Magnitude
                    cible = root
                end
            end
        end
        
        if cible then
            camera.CFrame = camera.CFrame:Lerp(CFrame.new(camera.CFrame.Position, cible.Position), vitesseSuivi)
            if distMin <= distAttaque and not enCombo then
                task.spawn(executerKyotoCombo)
            end
        end
    end
end)
