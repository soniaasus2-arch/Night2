--[[
    DAVIROBLOX HUB | Night 2
    - Mutant = Larry
]]

local player = game.Players.LocalPlayer
local mouse = player:GetMouse()

-- ========== TELEPORT ==========
local function teleportTo(pos)
    local char = player.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        char.HumanoidRootPart.CFrame = CFrame.new(pos)
        print("✅ Teleportado!")
    end
end

-- ========== INFINITE STAMINA ==========
local staminaActive = false
local staminaLoop = nil

local function setInfiniteStamina(state)
    staminaActive = state
    if staminaLoop then
        game:GetService("RunService").RenderStepped:Disconnect(staminaLoop)
        staminaLoop = nil
    end
    if state then
        staminaLoop = game:GetService("RunService").RenderStepped:Connect(function()
            local char = player.Character
            if char then
                local sprint = char:FindFirstChild("Sprint")
                if sprint then
                    if sprint:FindFirstChild("Overdrive") then
                        sprint.Overdrive.Value = 99999999
                    end
                    if sprint:FindFirstChild("Stamina") then
                        sprint.Stamina.Value = 100
                    end
                end
            end
        end)
        print("✅ Stamina Infinita ATIVA")
    else
        print("❌ Stamina Infinita DESATIVADA")
    end
end

-- ========== ESP PLAYERS ==========
local espActive = false
local espConnections = {}
local espObjects = {}

local function removeESP()
    for _, obj in pairs(espObjects) do
        if obj and obj.Parent then
            obj:Destroy()
        end
    end
    espObjects = {}
    for _, conn in pairs(espConnections) do
        if conn then
            conn:Disconnect()
        end
    end
    espConnections = {}
end

local function addESPToPlayer(plr)
    if plr == player then return end
    
    local function createESP(character)
        if not character or character:FindFirstChild("ESP_Highlight") then return end
        
        local highlight = Instance.new("Highlight")
        highlight.Name = "ESP_Highlight"
        highlight.FillColor = Color3.fromRGB(255, 0, 0)
        highlight.OutlineColor = Color3.fromRGB(200, 0, 0)
        highlight.FillTransparency = 0.5
        highlight.Adornee = character
        highlight.Parent = character
        
        espObjects[plr] = highlight
    end
    
    if plr.Character then
        createESP(plr.Character)
    end
    
    local conn = plr.CharacterAdded:Connect(createESP)
    espConnections[plr] = conn
end

local function setESP(state)
    espActive = state
    if state then
        removeESP()
        for _, plr in pairs(game.Players:GetPlayers()) do
            addESPToPlayer(plr)
        end
        game.Players.PlayerAdded:Connect(addESPToPlayer)
        print("✅ ESP ATIVADO")
    else
        removeESP()
        print("❌ ESP DESATIVADO")
    end
end

-- ========== FULLBRIGHT ==========
local fullbrightActive = false
local originalLighting = {}

local function setFullbright(state)
    fullbrightActive = state
    local lighting = game:GetService("Lighting")
    
    if state then
        originalLighting = {
            Ambient = lighting.Ambient,
            OutdoorAmbient = lighting.OutdoorAmbient,
            Brightness = lighting.Brightness,
            GlobalShadows = lighting.GlobalShadows,
            FogEnd = lighting.FogEnd
        }
        lighting.Ambient = Color3.fromRGB(255, 255, 255)
        lighting.OutdoorAmbient = Color3.fromRGB(255, 255, 255)
        lighting.Brightness = 2
        lighting.GlobalShadows = false
        lighting.FogEnd = 100000
        print("✅ Fullbright ATIVADO")
    else
        lighting.Ambient = originalLighting.Ambient or Color3.fromRGB(128, 128, 128)
        lighting.OutdoorAmbient = originalLighting.OutdoorAmbient or Color3.fromRGB(128, 128, 128)
        lighting.Brightness = originalLighting.Brightness or 1
        lighting.GlobalShadows = originalLighting.GlobalShadows or true
        lighting.FogEnd = originalLighting.FogEnd or 1000
        print("❌ Fullbright DESATIVADO")
    end
end

-- ========== ASSUSTAR MUTANT (LARRY) ==========
local function scareMutant()
    local mutant = nil
    
    -- Procura pelo Mutant (nome correto)
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj.Name == "Mutant" then
            mutant = obj
            break
        end
    end
    
    -- Se não achou, procura no ReplicatedStorage
    if not mutant then
        for _, obj in pairs(game.ReplicatedStorage:GetDescendants()) do
            if obj.Name == "Mutant" then
                mutant = obj
                break
            end
        end
    end
    
    if mutant then
        -- Faz o Mutant pular (susto)
        local hum = mutant:FindFirstChild("Humanoid")
        if hum then
            hum.Jump = true
        end
        
        -- Som de susto
        local sound = Instance.new("Sound")
        sound.SoundId = "rbxassetid://9120386438"
        sound.Volume = 0.8
        sound.Parent = mutant
        sound:Play()
        
        -- Efeito visual de flash
        local flash = Instance.new("Frame")
        flash.Size = UDim2.new(1, 0, 1, 0)
        flash.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
        flash.BackgroundTransparency = 0.5
        flash.Parent = player.PlayerGui
        task.wait(0.1)
        flash:Destroy()
        
        -- Stalker fica mais rápido
        local stalker = workspace:FindFirstChild("Stalker")
        if stalker and stalker:FindFirstChild("Humanoid") then
            local originalSpeed = stalker.Humanoid.WalkSpeed
            stalker.Humanoid.WalkSpeed = 25
            task.wait(4)
            stalker.Humanoid.WalkSpeed = originalSpeed or 16
        end
        
        print("💀 Mutant assustado!")
    else
        print("❌ Mutant não encontrado")
    end
end

-- ========== CRIAR GUI ==========
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "DaviRobloxHub"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Botão quadrado (daviroblox2023r)
local toggleBtn = Instance.new("TextButton")
toggleBtn.Size = UDim2.new(0, 60, 0, 60)
toggleBtn.Position = UDim2.new(0.02, 0, 0.5, -30)
toggleBtn.Text = "📱"
toggleBtn.TextSize = 30
toggleBtn.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
toggleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleBtn.BorderSizePixel = 0
toggleBtn.Parent = screenGui

-- Menu
local menu = Instance.new("Frame")
menu.Size = UDim2.new(0, 320, 0, 480)
menu.Position = UDim2.new(0.08, 0, 0.5, -240)
menu.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
menu.BackgroundTransparency = 0.05
menu.BorderSizePixel = 0
menu.Visible = false
menu.Parent = screenGui

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 40)
title.Text = "DAVIROBLOX HUB | Night 2"
title.TextSize = 18
title.TextColor3 = Color3.fromRGB(255, 200, 100)
title.BackgroundColor3 = Color3.fromRGB(30, 30, 45)
title.Font = Enum.Font.GothamBold
title.Parent = menu

local scroll = Instance.new("ScrollingFrame")
scroll.Size = UDim2.new(1, 0, 1, -40)
scroll.Position = UDim2.new(0, 0, 0, 40)
scroll.BackgroundTransparency = 1
scroll.ScrollBarThickness = 6
scroll.CanvasSize = UDim2.new(0, 0, 0, 0)
scroll.Parent = menu

local layout = Instance.new("UIListLayout")
layout.Padding = UDim.new(0, 8)
layout.SortOrder = Enum.SortOrder.LayoutOrder
layout.Parent = scroll

local function addButton(text, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.9, 0, 0, 35)
    btn.Text = text
    btn.TextSize = 14
    btn.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.BorderSizePixel = 0
    btn.MouseButton1Click:Connect(callback)
    btn.Parent = scroll
    return btn
end

local function addToggle(text, onChange)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0.9, 0, 0, 35)
    frame.BackgroundTransparency = 1
    frame.Parent = scroll
    
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0.7, 0, 1, 0)
    label.Text = text
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.TextColor3 = Color3.fromRGB(220, 220, 220)
    label.BackgroundTransparency = 1
    label.TextSize = 14
    label.Parent = frame
    
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.25, 0, 0.8, 0)
    btn.Position = UDim2.new(0.72, 0, 0.1, 0)
    btn.Text = "Off"
    btn.TextSize = 13
    btn.BackgroundColor3 = Color3.fromRGB(80, 80, 100)
    btn.TextColor3 = Color3.fromRGB(255, 100, 100)
    btn.BorderSizePixel = 0
    btn.Parent = frame
    
    local state = false
    btn.MouseButton1Click:Connect(function()
        state = not state
        btn.Text = state and "On" or "Off"
        btn.BackgroundColor3 = state and Color3.fromRGB(60, 150, 60) or Color3.fromRGB(80, 80, 100)
        btn.TextColor3 = state and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
        onChange(state)
    end)
    return frame
end

-- ========== MONTAR MENU ==========

-- Seção Teleports
addButton("📍 Teleport to Hall", function() teleportTo(Vector3.new(-100, 10, 50)) end)
addButton("📍 Teleport Outside", function() teleportTo(Vector3.new(-120, 10, 80)) end)
addButton("📍 Teleport Radio 1", function() teleportTo(Vector3.new(-50, 10, -30)) end)
addButton("📍 Teleport Radio 2", function() teleportTo(Vector3.new(-60, 10, -40)) end)
addButton("📍 Teleport Delivery Screen", function() teleportTo(Vector3.new(-30, 10, 0)) end)
addButton("📍 Teleport Radio Tower", function() teleportTo(Vector3.new(-80, 30, -60)) end)
addButton("📍 Teleport Power Storage", function() teleportTo(Vector3.new(-10, 5, 20)) end)
addButton("📍 Teleport Power", function() teleportTo(Vector3.new(0, 5, 30)) end)

-- Seção Toggles
addToggle("⚡ Infinite Stamina", setInfiniteStamina)
addToggle("👁️ ESP Players", setESP)
addToggle("💡 Fullbright", setFullbright)

-- Botão Especial
addButton("💀 ASSUSTAR MUTANT", scareMutant)

-- Ajustar Scroll
local function updateCanvas()
    local children = scroll:GetChildren()
    local total = 0
    for _, child in pairs(children) do
        if child:IsA("TextButton") or child:IsA("Frame") then
            total = total + 36
        end
    end
    scroll.CanvasSize = UDim2.new(0, 0, 0, total + 20)
end
task.wait(0.1)
updateCanvas()

-- Abrir/fechar menu
local menuOpen = false
toggleBtn.MouseButton1Click:Connect(function()
    menuOpen = not menuOpen
    menu.Visible = menuOpen
    if menuOpen then updateCanvas() end
end)

-- Fechar ao clicar fora
mouse.Button1Down:Connect(function()
    if menuOpen then
        local mPos = Vector2.new(mouse.X, mouse.Y)
        local mAbsPos = menu.AbsolutePosition
        local mSize = menu.AbsoluteSize
        local bAbsPos = toggleBtn.AbsolutePosition
        local bSize = toggleBtn.AbsoluteSize
        
        if not (mPos.X >= mAbsPos.X and mPos.X <= mAbsPos.X + mSize.X and
                mPos.Y >= mAbsPos.Y and mPos.Y <= mAbsPos.Y + mSize.Y) and
           not (mPos.X >= bAbsPos.X and mPos.X <= bAbsPos.X + bSize.X and
                mPos.Y >= bAbsPos.Y and mPos.Y <= bAbsPos.Y + bSize.Y) then
            menu.Visible = false
            menuOpen = false
        end
    end
end)

print("✅ DAVIROBLOX HUB carregado! Mutant = Larry")
