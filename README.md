local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local VirtualInputManager = game:GetService("VirtualInputManager")

local localPlayer = Players.LocalPlayer
local camera = workspace.CurrentCamera
local actif = false
local enCombo = false

-- --- RÉGLAGES PRÉCIS ---
local distAttaque = 15
local vitesseSuivi = 0.6
local dureeAttaque = 2.0 -- La durée que tu m'as donnée

-- --- INTERFACE MATTE ---
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
label.Text = "TSB COMBO: OFF"
label.Font = Enum.Font.GothamBold
label.TextSize = 14
label.TextColor3 = Color3.fromRGB(110, 110, 110)

-- --- SIMULATIONS ---
local function press(key)
    VirtualInputManager:SendKeyEvent(true, key, false, game)
    task.wait(0.05)
    VirtualInputManager:SendKeyEvent(false, key, false, game)
end

-- --- SÉQUENCE DE COMBAT (TIMING 2s) ---
local function executerCombo()
    if enCombo then return end
    enCombo = true
    
    -- 1. Lancement Attaque 1 (Touche 1)
    press(Enum.KeyCode.One)
    
    -- On attend que l'attaque se finisse (environ 1.8s pour anticiper le dash)
    task.wait(dureeAttaque - 0.2)
    
    -- 2. Dash (Touche Q) pour annuler la fin de l'anim ou se replacer
    press(Enum.KeyCode.Q)
    task.wait(0.3) -- Temps du dash
    
    -- 3. Lancement Attaque 2 (Touche 2)
    press(Enum.KeyCode.Two)
    
    -- On attend que la deuxième attaque se finisse aussi
    task.wait(dureeAttaque)
    
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
            label.Text = actif and "TSB COMBO: ON" or "TSB COMBO: OFF"
            label.TextColor3 = actif and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(110, 110, 110)
        end
    end
end)

-- --- BOUCLE ---
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
            -- Suivi caméra et perso
            camera.CFrame = camera.CFrame:Lerp(CFrame.new(camera.CFrame.Position, cible.Position), vitesseSuivi)
            hum:Move((cible.Position - myRoot.Position).Unit, true)
            
            -- Lancer le combo à 2 secondes si à portée
            if distMin <= distAttaque and not enCombo then
                task.spawn(executerCombo)
            end
        end
    end
end)
