-- --- CHARGEMENT DE L'INTERFACE WINDUI ---
local WindUI = loadstring(game:HttpGet('https://github.com/Footagesus/WindUI/releases/latest/download/main.lua'))()

-- --- SERVICES ---
local Players = game:GetService('Players')
local RunService = game:GetService('RunService')
local VirtualInputManager = game:GetService('VirtualInputManager')
local localPlayer = Players.LocalPlayer

-- --- VARIABLES DE CONFIGURATION ---
_G.KyotoEnabled = false
_G.KyotoDelay = 5000 -- Tes 5 secondes
_G.DashDelay = 2000  -- Tes 2 secondes
_G.EnCombo = false

-- --- THÈME BLOOD SHADOW ---
local Window = WindUI:CreateWindow({
    Title = "Dovi's Hub v1.1",
    Icon = "rbxassetid://10888673661",
    Author = "Dovi",
    Size = UDim2.new(0, 450, 0, 350)
})

-- --- LOGIQUE DU COMBO (Ton timing exact) ---
local function executeKyoto()
    if _G.EnCombo then return end
    _G.EnCombo = true
    
    -- Maintien de l'avance (Anti-recul)
    VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.W, false, game)

    -- ATTAQUE 1
    VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.One, false, game)
    task.wait(0.05)
    VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.One, false, game)
    task.wait(_G.KyotoDelay / 1000 - 0.2)

    -- DASH
    VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.Q, false, game)
    task.wait(0.05)
    VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.Q, false, game)
    task.wait(0.1)
    VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.W, false, game) -- Force l'anti-recul
    task.wait(_G.DashDelay / 1000 - 0.1)

    -- ATTAQUE 2
    VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.Two, false, game)
    task.wait(0.05)
    VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.Two, false, game)
    task.wait(_G.KyotoDelay / 1000)

    VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.W, false, game)
    _G.EnCombo = false
end

-- --- ONGLETS ---
local MainTab = Window:Tab("Combat")

MainTab:Toggle({
    Title = "Kyoto Combo",
    Default = false,
    Callback = function(state)
        _G.KyotoEnabled = state
    end
})

MainTab:Slider({
    Title = "Kyoto Delay (ms)",
    Min = 1000,
    Max = 10000,
    Default = 5000,
    Callback = function(value)
        _G.KyotoDelay = value
    end
})

-- --- BOUCLE DE DÉTECTION ---
RunService.RenderStepped:Connect(function()
    if _G.KyotoEnabled and not _G.EnCombo then
        local myChar = localPlayer.Character
        if myChar and myChar:FindFirstChild("HumanoidRootPart") then
            -- Détection automatique à 12 studs
            for _, v in pairs(workspace:GetDescendants()) do
                if v:IsA("Humanoid") and v.Parent ~= myChar and v.Health > 0 then
                    local root = v.Parent:FindFirstChild("HumanoidRootPart")
                    if root then
                        local dist = (root.Position - myChar.HumanoidRootPart.Position).Magnitude
                        if dist < 12 then
                            task.spawn(executeKyoto)
                        end
                    end
                end
            end
        end
    end
end)
