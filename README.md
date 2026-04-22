local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local VirtualInputManager = game:GetService("VirtualInputManager")

local localPlayer = Players.LocalPlayer
local camera = workspace.CurrentCamera
local actif = false
local enCombo = false

-- --- RÉGLAGES CHRONO RÉELS ---
local distAttaque = 15
local vitesseSuivi = 0.6
local dureeAttaque = 5.0 -- 5 secondes par attaque
local dureeDash = 2.0    -- 2 secondes pour le dash

-- Nettoyage des anciennes interfaces
for _, v in pairs(localPlayer.PlayerGui:GetChildren()) do
    if v.Name == "TennySystem" then v:Destroy() end
end

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
label.Text = "TSB ULTIMATE: OFF"
label.Font = Enum.Font.GothamBold
label.TextSize = 14
label.TextColor3 = Color3.fromRGB(110, 110, 110)

-- --- SIMULATION DE TOUCHES ---
local function press(key)
    VirtualInputManager:SendKeyEvent(true, key, false, game)
    task.wait(0.05)
    VirtualInputManager:SendKeyEvent(false, key, false, game)
end

-- --- SÉQUENCE DE COMBO (SANS RECUL) ---
local function executerCombo()
    if enCombo then return end
    enCombo = true
    
    -- On force la marche avant pour que le Dash aille vers l'ennemi
    VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.W, false, game)

    -- 1. Première Attaque
    press(Enum.KeyCode.One)
    task.wait(dureeAttaque - 0.2) 
    
    -- 2. Dash (ira vers l'avant car W est maintenu)
    press(Enum.KeyCode.Q)
    task.wait(dureeDash) 
    
    -- 3. Deuxième Attaque
    press(Enum.KeyCode.Two)
    task.wait(dureeAttaque)
    
    -- On relâche la marche avant à la fin du combo
    VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.W, false, game)
    
    enCombo = false
end

-- --- DÉTECTION TACTILE / CLIC ---
UserInputService.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
        local pos = input.Position
        local bPos = frame.AbsolutePosition
        local bSize = frame.AbsoluteSize
        
        if pos.X >= bPos.X and pos.X <= (bPos.X + bSize.X) and
           pos.Y >= bPos.Y and pos.Y <= (bPos.Y + bSize.Y) then
            
            actif = not actif
            label.Text = actif and "TSB ULTIMATE: ON" or "TSB ULTIMATE: OFF"
            label.TextColor3 = actif and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(110, 110, 110)
            frame.BorderColor3 = actif and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(130, 0, 0)
        end
    end
end)

-- --- BOUCLE PRINCIPALE (VISÉE + MOUVEMENT + COMBO) ---
RunService.RenderStepped:Connect(function()
    if actif and localPlayer.Character and localPlayer.Character:FindFirstChild("Humanoid") then
        local myRoot = localPlayer.Character.PrimaryPart
        local hum = localPlayer.Character.Humanoid
        
        local cible = nil
        local distMin = 100
        
        -- Recherche des NPCs ou Joueurs
        for _, v in pairs(workspace:GetDescendants()) do
            if v:IsA("Humanoid") and v.Parent ~= localPlayer.Character and v.Health > 0 then
                local root = v.Parent:FindFirstChild("HumanoidRootPart")
                if root then
                    local d = (root.Position - myRoot.Position).Magnitude
                    if d < distMin then
                        distMin = d
                        cible = root
                    end
                end
            end
        end
        
        if cible then
            -- 1. Toujours regarder la cible
            camera.CFrame = camera.CFrame:Lerp(CFrame.new(camera.CFrame.Position, cible.Position), vitesseSuivi)
            
            -- 2. Avancer vers la cible (Suivi auto)
            local direction = (cible.Position - myRoot.Position).Unit
            hum:Move(Vector3.new(direction.X, 0, direction.Z), true)
            
            -- 3. Lancer le combo si à portée et pas déjà en cours
            if distMin <= distAttaque and not enCombo then
                task.spawn(executerCombo)
            end
        end
    end
end)
