local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local VirtualInputManager = game:GetService("VirtualInputManager")

local localPlayer = Players.LocalPlayer
local camera = workspace.CurrentCamera
local actif = false

-- --- CONFIGURATION COMBAT ---
local distDetection = 100
local distAttaqueBase = 12
local distCapacites = 25 -- Les skills portent souvent plus loin
local vitesseSuivi = 0.6

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
label.Text = "FULL AUTO-ATTACK: OFF"
label.Font = Enum.Font.GothamBold
label.TextSize = 14
label.TextColor3 = Color3.fromRGB(110, 110, 110)

-- --- ACTIVATION ---
UserInputService.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
        local pos = input.Position
        local bPos = frame.AbsolutePosition
        local bSize = frame.AbsoluteSize
        if pos.X >= bPos.X and pos.X <= (bPos.X + bSize.X) and pos.Y >= bPos.Y and pos.Y <= (bPos.Y + bSize.Y) then
            actif = not actif
            label.Text = actif and "FULL AUTO-ATTACK: ON" or "FULL AUTO-ATTACK: OFF"
            label.TextColor3 = actif and Color3.fromRGB(0, 200, 0) or Color3.fromRGB(110, 110, 110)
            frame.BorderColor3 = actif and Color3.fromRGB(0, 200, 0) or Color3.fromRGB(130, 0, 0)
        end
    end
end)

-- --- FONCTION POUR PRESSER LES TOUCHES ---
local function pressKey(key)
    VirtualInputManager:SendKeyEvent(true, key, false, game)
    task.wait(0.05)
    VirtualInputManager:SendKeyEvent(false, key, false, game)
end

-- --- BOUCLE DE COMBAT ---
RunService.RenderStepped:Connect(function()
    if actif and localPlayer.Character and localPlayer.Character:FindFirstChild("Humanoid") then
        local myRoot = localPlayer.Character.PrimaryPart
        local hum = localPlayer.Character.Humanoid
        
        local cible = nil
        local distancePlusProche = distDetection
        
        -- Recherche Proximité (Players + NPCs)
        for _, v in pairs(workspace:GetDescendants()) do
            if v:IsA("Humanoid") and v.Parent ~= localPlayer.Character and v.Health > 0 then
                local rootPart = v.Parent:FindFirstChild("HumanoidRootPart")
                if rootPart then
                    local dist = (rootPart.Position - myRoot.Position).Magnitude
                    if dist < distancePlusProche then
                        distancePlusProche = dist
                        cible = rootPart
                    end
                end
            end
        end
        
        if cible then
            -- 1. Visée et Suivi
            camera.CFrame = camera.CFrame:Lerp(CFrame.new(camera.CFrame.Position, cible.Position), vitesseSuivi)
            local dir = (cible.Position - myRoot.Position).Unit
            hum:Move(Vector3.new(dir.X, 0, dir.Z), true)
            
            -- 2. Attaque de base (Clic)
            if distancePlusProche <= distAttaqueBase then
                VirtualInputManager:SendMouseButtonEvent(0, 0, 0, true, game, 0)
                VirtualInputManager:SendMouseButtonEvent(0, 0, 0, false, game, 0)
            end
            
            -- 3. Utilisation des Capacités (Touches 1, 2, 3, 4)
            -- On les lance si l'ennemi est à portée de skill
            if distancePlusProche <= distCapacites then
                -- On utilise task.spawn pour ne pas ralentir le reste du script
                task.spawn(function()
                    pressKey(Enum.KeyCode.One)
                    task.wait(0.1)
                    pressKey(Enum.KeyCode.Two)
                    task.wait(0.1)
                    pressKey(Enum.KeyCode.Three)
                    task.wait(0.1)
                    pressKey(Enum.KeyCode.Four)
                end)
            end
        end
    end
end)

local frame = Instance.new("Frame", screenGui)
frame.Size = UDim2.new(0, 200, 0, 70)
frame.Position = UDim2.new(0.5, -100, 0.1, 0)
frame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
frame.BorderSizePixel = 2
frame.BorderColor3 = Color3.fromRGB(130, 0, 0)

local label = Instance.new("TextLabel", frame)
label.Size = UDim2.new(1, 0, 1, 0)
label.BackgroundTransparency = 1
label.Text = "TEST NPC: OFF"
label.Font = Enum.Font.GothamBold
label.TextSize = 16
label.TextColor3 = Color3.fromRGB(110, 110, 110)

-- --- ACTIVATION ---
UserInputService.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
        local pos = input.Position
        local bPos = frame.AbsolutePosition
        local bSize = frame.AbsoluteSize
        if pos.X >= bPos.X and pos.X <= (bPos.X + bSize.X) and pos.Y >= bPos.Y and pos.Y <= (bPos.Y + bSize.Y) then
            actif = not actif
            label.Text = actif and "TEST NPC: ON" or "TEST NPC: OFF"
            label.TextColor3 = actif and Color3.fromRGB(0, 200, 0) or Color3.fromRGB(110, 110, 110)
            frame.BorderColor3 = actif and Color3.fromRGB(0, 200, 0) or Color3.fromRGB(130, 0, 0)
        end
    end
end)

-- --- BOUCLE DE TEST ---
RunService.RenderStepped:Connect(function()
    if actif and localPlayer.Character and localPlayer.Character:FindFirstChild("Humanoid") then
        local myRoot = localPlayer.Character.PrimaryPart
        local hum = localPlayer.Character.Humanoid
        
        local cible = nil
        local distancePlusProche = distDetection
        
        -- Recherche dans workspace (pour inclure les NPCs/Dummies)
        for _, v in pairs(workspace:GetDescendants()) do
            if v:IsA("Humanoid") and v.Parent ~= localPlayer.Character then
                local rootPart = v.Parent:FindFirstChild("HumanoidRootPart")
                if rootPart and v.Health > 0 then
                    local dist = (rootPart.Position - myRoot.Position).Magnitude
                    if dist < distancePlusProche then
                        distancePlusProche = dist
                        cible = rootPart
                    end
                end
            end
        end
        
        if cible then
            -- 1. Regarder le NPC
            camera.CFrame = camera.CFrame:Lerp(CFrame.new(camera.CFrame.Position, cible.Position), vitesseSuivi)
            
            -- 2. Avancer vers lui
            local dir = (cible.Position - myRoot.Position).Unit
            hum:Move(Vector3.new(dir.X, 0, dir.Z), true)
            
            -- 3. Auto-Attack
            if distancePlusProche <= distFrappe then
                VirtualInputManager:SendMouseButtonEvent(0, 0, 0, true, game, 0)
                task.wait(0.05) -- Petit délai pour simuler un vrai clic
                VirtualInputManager:SendMouseButtonEvent(0, 0, 0, false, game, 0)
            end
        end
    end
end)
