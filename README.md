-- ==========================================
-- DAVI HUB - RESIDENCE MASSACRE (COMPLETO)
-- ==========================================

local player = game.Players.LocalPlayer
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local Camera = workspace.CurrentCamera
local UserInputService = game:GetService("UserInputService")
local RS = game.ReplicatedStorage

-- ============================================================
-- CRIA GUI (SEM FUNDO PRETO)
-- ============================================================
local gui = Instance.new("ScreenGui")
gui.Name = "DaviHub"
gui.Parent = player:WaitForChild("PlayerGui")
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true

-- JANELA PRINCIPAL (CENTRO)
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 480, 0, 500)
mainFrame.Position = UDim2.new(0.5, -240, 0.15, 0)
mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
mainFrame.BackgroundTransparency = 0.1
mainFrame.BorderSizePixel = 0
mainFrame.ClipsDescendants = true
mainFrame.Parent = gui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 16)
corner.Parent = mainFrame

local border = Instance.new("UIStroke")
border.Color = Color3.fromRGB(255, 140, 0)
border.Thickness = 2
border.Transparency = 0.4
border.Parent = mainFrame

-- ============================================================
-- CABEÇALHO (ARRASTÁVEL)
-- ============================================================
local header = Instance.new("Frame")
header.Size = UDim2.new(1, 0, 0, 45)
header.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
header.BackgroundTransparency = 0.25
header.BorderSizePixel = 0
header.Parent = mainFrame

local headerCorner = Instance.new("UICorner")
headerCorner.CornerRadius = UDim.new(0, 16)
headerCorner.Parent = header

local titulo = Instance.new("TextLabel")
titulo.Size = UDim2.new(1, -80, 1, 0)
titulo.Position = UDim2.new(0, 15, 0, 0)
titulo.Text = "✦ DAVI HUB"
titulo.TextColor3 = Color3.fromRGB(255, 255, 255)
titulo.TextSize = 18
titulo.Font = Enum.Font.GothamBold
titulo.BackgroundTransparency = 1
titulo.TextXAlignment = Enum.TextXAlignment.Left
titulo.Parent = header

-- MINIMIZAR
local minBtn = Instance.new("TextButton")
minBtn.Size = UDim2.new(0, 32, 0, 32)
minBtn.Position = UDim2.new(1, -70, 0, 6.5)
minBtn.Text = "−"
minBtn.TextSize = 22
minBtn.BackgroundColor3 = Color3.fromRGB(80, 80, 100)
minBtn.BackgroundTransparency = 0.5
minBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
minBtn.Font = Enum.Font.GothamBold
minBtn.BorderSizePixel = 0
minBtn.Parent = header

local minCorner = Instance.new("UICorner")
minCorner.CornerRadius = UDim.new(0, 8)
minCorner.Parent = minBtn

local minimized = false
minBtn.MouseButton1Click:Connect(function()
    minimized = not minimized
    mainFrame.Size = minimized and UDim2.new(0, 480, 0, 45) or UDim2.new(0, 480, 0, 500)
    minBtn.Text = minimized and "+" or "−"
    for _, child in pairs(mainFrame:GetChildren()) do
        if child ~= header then
            child.Visible = not minimized
        end
    end
end)

-- FECHAR
local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 32, 0, 32)
closeBtn.Position = UDim2.new(1, -38, 0, 6.5)
closeBtn.Text = "✕"
closeBtn.TextSize = 18
closeBtn.BackgroundColor3 = Color3.fromRGB(200, 60, 60)
closeBtn.BackgroundTransparency = 0.4
closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
closeBtn.Font = Enum.Font.GothamBold
closeBtn.BorderSizePixel = 0
closeBtn.Parent = header

local closeCorner = Instance.new("UICorner")
closeCorner.CornerRadius = UDim.new(0, 8)
closeCorner.Parent = closeBtn

closeBtn.MouseButton1Click:Connect(function()
    gui:Destroy()
end)

-- ============================================================
-- ARRASTAR
-- ============================================================
local dragging = false
local dragInput, dragStart, startPos

header.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = mainFrame.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

header.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        dragInput = input
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and input == dragInput then
        local delta = input.Position - dragStart
        mainFrame.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
    end
end)

-- ============================================================
-- CONTEÚDO
-- ============================================================
local contentFrame = Instance.new("Frame")
contentFrame.Size = UDim2.new(1, 0, 1, -45)
contentFrame.Position = UDim2.new(0, 0, 0, 45)
contentFrame.BackgroundTransparency = 1
contentFrame.Parent = mainFrame

-- ============================================================
-- ABAS LATERAIS
-- ============================================================
local sidebar = Instance.new("Frame")
sidebar.Size = UDim2.new(0, 85, 1, 0)
sidebar.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
sidebar.BackgroundTransparency = 0.3
sidebar.BorderSizePixel = 0
sidebar.Parent = contentFrame

local sidebarCorner = Instance.new("UICorner")
sidebarCorner.CornerRadius = UDim.new(0, 12)
sidebarCorner.Parent = sidebar

local sidebarLayout = Instance.new("UIListLayout")
sidebarLayout.Padding = UDim.new(0, 6)
sidebarLayout.SortOrder = Enum.SortOrder.LayoutOrder
sidebarLayout.Parent = sidebar

-- Lista de abas
local abaButtons = {}
local abaFrames = {}
local abas = {
    {name = "🏠", label = "Menu"},
    {name = "👤", label = "Char"},
    {name = "📍", label = "Tele"},
    {name = "🌙", label = "N1"},
    {name = "🌙", label = "N2"},
    {name = "🎯", label = "N3"},
    {name = "👁️", label = "ESP"},
    {name = "⚡", label = "Auto"},
}

-- ============================================================
-- FUNÇÃO PARA CRIAR ABA (COM SCROLL)
-- ============================================================
local function criarAba(nome, icone)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.85, 0, 0, 45)
    btn.Position = UDim2.new(0.075, 0, 0, 0)
    btn.Text = icone .. "\n" .. nome
    btn.TextColor3 = Color3.fromRGB(200, 200, 200)
    btn.TextSize = 11
    btn.Font = Enum.Font.GothamBold
    btn.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
    btn.BackgroundTransparency = 0.4
    btn.BorderSizePixel = 0
    btn.Parent = sidebar

    local btnCorner = Instance.new("UICorner")
    btnCorner.CornerRadius = UDim.new(0, 10)
    btnCorner.Parent = btn

    -- SCROLLING FRAME
    local frame = Instance.new("ScrollingFrame")
    frame.Size = UDim2.new(1, -90, 1, 0)
    frame.Position = UDim2.new(0, 90, 0, 0)
    frame.BackgroundTransparency = 1
    frame.BorderSizePixel = 0
    frame.ScrollBarThickness = 8
    frame.ScrollBarImageColor3 = Color3.fromRGB(255, 140, 0)
    frame.ScrollBarImageTransparency = 0.3
    frame.VerticalScrollBarPosition = Enum.VerticalScrollBarPosition.Right
    frame.Visible = false
    frame.Parent = contentFrame
    frame.AutomaticCanvasSize = Enum.AutomaticSize.Y

    local flayout = Instance.new("UIListLayout")
    flayout.Padding = UDim.new(0, 6)
    flayout.SortOrder = Enum.SortOrder.LayoutOrder
    flayout.Parent = frame

    flayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
        frame.CanvasSize = UDim2.new(0, 0, 0, flayout.AbsoluteContentSize.Y + 20)
    end)

    btn.MouseButton1Click:Connect(function()
        for _, b in pairs(abaButtons) do
            b.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
            b.BackgroundTransparency = 0.4
            b.TextColor3 = Color3.fromRGB(200, 200, 200)
        end
        btn.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
        btn.BackgroundTransparency = 0.3
        btn.TextColor3 = Color3.fromRGB(255, 255, 255)
        for _, f in pairs(abaFrames) do
            f.Visible = false
        end
        frame.Visible = true
        task.wait(0.05)
        frame.CanvasSize = UDim2.new(0, 0, 0, flayout.AbsoluteContentSize.Y + 20)
    end)

    table.insert(abaButtons, btn)
    table.insert(abaFrames, frame)
    return frame, btn
end

-- Criar todas as abas
local abaFramesMap = {}
for i, aba in pairs(abas) do
    local frame, btn = criarAba(aba.label, aba.name)
    abaFramesMap[aba.label] = frame
    if i == 1 then
        btn.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
        btn.BackgroundTransparency = 0.3
        btn.TextColor3 = Color3.fromRGB(255, 255, 255)
        frame.Visible = true
    end
end

-- ============================================================
-- FUNÇÕES AUXILIARES
-- ============================================================
local function addCard(parent)
    local card = Instance.new("Frame")
    card.Size = UDim2.new(0.92, 0, 0, 0)
    card.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
    card.BackgroundTransparency = 0.3
    card.BorderSizePixel = 0
    card.AutomaticSize = Enum.AutomaticSize.Y
    card.Parent = parent

    local cardCorner = Instance.new("UICorner")
    cardCorner.CornerRadius = UDim.new(0, 12)
    cardCorner.Parent = card

    local cardLayout = Instance.new("UIListLayout")
    cardLayout.Padding = UDim.new(0, 4)
    cardLayout.SortOrder = Enum.SortOrder.LayoutOrder
    cardLayout.Parent = card

    return card
end

local function addLabel(parent, text, color)
    local l = Instance.new("TextLabel")
    l.Size = UDim2.new(1, 0, 0, 25)
    l.Text = text
    l.TextColor3 = color or Color3.fromRGB(255, 200, 100)
    l.TextSize = 14
    l.Font = Enum.Font.GothamBold
    l.BackgroundTransparency = 1
    l.Parent = parent
    return l
end

local function addButton(parent, text, callback)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(0.95, 0, 0, 35)
    b.Text = text
    b.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
    b.BackgroundTransparency = 0.2
    b.TextColor3 = Color3.fromRGB(255, 255, 255)
    b.Font = Enum.Font.Gotham
    b.TextSize = 13
    b.BorderSizePixel = 0
    b.AutomaticSize = Enum.AutomaticSize.Y
    b.Parent = parent

    local bCorner = Instance.new("UICorner")
    bCorner.CornerRadius = UDim.new(0, 8)
    bCorner.Parent = b

    b.MouseEnter:Connect(function() b.BackgroundTransparency = 0 end)
    b.MouseLeave:Connect(function() b.BackgroundTransparency = 0.2 end)
    b.MouseButton1Click:Connect(callback)
    return b
end

local function addToggle(parent, text, callback, default)
    local c = Instance.new("Frame")
    c.Size = UDim2.new(0.95, 0, 0, 35)
    c.BackgroundTransparency = 1
    c.AutomaticSize = Enum.AutomaticSize.Y
    c.Parent = parent

    local l = Instance.new("TextLabel")
    l.Size = UDim2.new(0.65, 0, 1, 0)
    l.Text = text
    l.TextColor3 = Color3.fromRGB(220, 220, 220)
    l.TextSize = 13
    l.Font = Enum.Font.Gotham
    l.BackgroundTransparency = 1
    l.TextXAlignment = Enum.TextXAlignment.Left
    l.Parent = c

    local b = Instance.new("TextButton")
    b.Size = UDim2.new(0.25, 0, 0.8, 0)
    b.Position = UDim2.new(0.72, 0, 0.1, 0)
    b.Text = default and "ON" or "OFF"
    b.BackgroundColor3 = default and Color3.fromRGB(255, 140, 0) or Color3.fromRGB(80, 80, 100)
    b.TextColor3 = default and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(255, 100, 100)
    b.Font = Enum.Font.GothamBold
    b.TextSize = 12
    b.BorderSizePixel = 0
    b.Parent = c

    local bCorner = Instance.new("UICorner")
    bCorner.CornerRadius = UDim.new(0, 6)
    bCorner.Parent = b

    local st = default or false
    b.MouseButton1Click:Connect(function()
        st = not st
        b.Text = st and "ON" or "OFF"
        b.BackgroundColor3 = st and Color3.fromRGB(255, 140, 0) or Color3.fromRGB(80, 80, 100)
        b.TextColor3 = st and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(255, 100, 100)
        callback(st)
    end)
    return c
end

local function addSlider(parent, text, callback, min, max, default)
    local c = Instance.new("Frame")
    c.Size = UDim2.new(0.95, 0, 0, 50)
    c.BackgroundTransparency = 1
    c.Parent = parent

    local l = Instance.new("TextLabel")
    l.Size = UDim2.new(1, 0, 0, 20)
    l.Text = text .. " (" .. default .. ")"
    l.TextColor3 = Color3.fromRGB(220, 220, 220)
    l.TextSize = 13
    l.Font = Enum.Font.Gotham
    l.BackgroundTransparency = 1
    l.Parent = c

    local sf = Instance.new("Frame")
    sf.Size = UDim2.new(1, 0, 0, 14)
    sf.Position = UDim2.new(0, 0, 0, 22)
    sf.BackgroundColor3 = Color3.fromRGB(60, 60, 80)
    sf.BorderSizePixel = 0
    sf.Parent = c

    local sfCorner = Instance.new("UICorner")
    sfCorner.CornerRadius = UDim.new(0, 7)
    sfCorner.Parent = sf

    local sb = Instance.new("TextButton")
    sb.Size = UDim2.new(0, 16, 1, 0)
    sb.Position = UDim2.new((default - min) / (max - min), -8, 0, 0)
    sb.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
    sb.Text = ""
    sb.BorderSizePixel = 0
    sb.Parent = sf

    local sbCorner = Instance.new("UICorner")
    sbCorner.CornerRadius = UDim.new(0, 8)
    sbCorner.Parent = sb

    local vl = Instance.new("TextLabel")
    vl.Size = UDim2.new(0, 35, 1, 0)
    vl.Position = UDim2.new(1, -38, 0, 0)
    vl.Text = tostring(default)
    vl.TextColor3 = Color3.fromRGB(255, 255, 255)
    vl.TextSize = 12
    vl.Font = Enum.Font.Gotham
    vl.BackgroundTransparency = 1
    vl.Parent = sf

    local dragging = false
    local function update(input)
        local pos = input.Position.X
        local fp = sf.AbsolutePosition.X
        local fs = sf.AbsoluteSize.X
        local pct = math.clamp((pos - fp) / fs, 0, 1)
        local val = math.floor(min + pct * (max - min))
        sb.Position = UDim2.new(pct, -8, 0, 0)
        vl.Text = tostring(val)
        callback(val)
        l.Text = text .. " (" .. tostring(val) .. ")"
    end

    sb.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            update(input)
        end
    end)
    sb.InputEnded:Connect(function() dragging = false end)
    UserInputService.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            update(input)
        end
    end)
end

-- ============================================================
-- NOCLIP
-- ============================================================
local noclipLoop = nil
local function toggleNoclip(v)
    if noclipLoop then
        noclipLoop:Disconnect()
        noclipLoop = nil
    end
    if v then
        print("🔓 NOCLIP ATIVADO!")
        noclipLoop = RunService.Stepped:Connect(function()
            local char = player.Character
            if char then
                for _, part in pairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
            end
        end)
    else
        print("🔒 NOCLIP DESATIVADO!")
        local char = player.Character
        if char then
            for _, part in pairs(char:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = true
                end
            end
        end
    end
end

-- ============================================================
-- TELEPORTS
-- ============================================================
local teleportsN1 = {
    ["Ladder"] = CFrame.new(-0.173, 9.3, -81.32),
    ["Generator"] = CFrame.new(-79.722, 4.675, -131.918),
    ["Entrance"] = CFrame.new(-11.036, 7.73, -31.822),
    ["LivingRoom"] = CFrame.new(-34.962, 8.05, -47.153),
    ["Bedroom"] = CFrame.new(-32.645, 23.8, -72.845),
}
local teleportsN2 = {
    ["SafeSpot N2"] = CFrame.new(-339.321, 82.4, -40.622),
    ["DeliveryBoard"] = CFrame.new(-282.224, 82.4, 14.674),
    ["Main"] = CFrame.new(-304.235, 82.4, -6.777),
    ["Corridor1"] = CFrame.new(-303.846, 82.4, 50.169),
    ["Entrance2"] = CFrame.new(-217.417, 82.4, 65.412),
    ["Corridor2"] = CFrame.new(-293.11, 82.4, -89.501),
}
local teleportsN3 = {
    ["Cabana 1"] = CFrame.new(99.8, 4.5, -247.2),
    ["Cabana 2"] = CFrame.new(-36.9, 4.5, 68.7),
    ["Cabana 3"] = CFrame.new(-31.7, 4.5, 268.8),
    ["Cabana 4"] = CFrame.new(233.6, 4.5, 245.8),
    ["Cutscene Room"] = CFrame.new(-237, -22.5, 107),
    ["Safe Spot N3"] = CFrame.new(194, 38.7, -217.4),
    ["Lodge"] = CFrame.new(-226.8, 17.4, 103.7),
    ["Jeffry Canna"] = CFrame.new(177.5, 4.3, 197.9),
}

local function teleportar(cframe)
    local char = player.Character or player.CharacterAdded:Wait()
    if char and char:FindFirstChild("HumanoidRootPart") then
        char.HumanoidRootPart.CFrame = cframe
    end
end

-- ============================================================
-- FUNÇÕES CHARACTER
-- ============================================================
local origLight = {}
local function toggleFullbright(v)
    local li = Lighting
    if v then
        origLight = {
            Ambient = li.Ambient,
            OutdoorAmbient = li.OutdoorAmbient,
            Brightness = li.Brightness,
            GlobalShadows = li.GlobalShadows,
            FogEnd = li.FogEnd
        }
        li.Ambient = Color3.fromRGB(255, 255, 255)
        li.OutdoorAmbient = Color3.fromRGB(255, 255, 255)
        li.Brightness = 2
        li.GlobalShadows = false
        li.FogEnd = 100000
    else
        li.Ambient = origLight.Ambient or Color3.fromRGB(128, 128, 128)
        li.OutdoorAmbient = origLight.OutdoorAmbient or Color3.fromRGB(128, 128, 128)
        li.Brightness = origLight.Brightness or 1
        li.GlobalShadows = origLight.GlobalShadows or true
        li.FogEnd = origLight.FogEnd or 1000
    end
end

local staminaLoop = nil
local function toggleStamina(v)
    if staminaLoop then staminaLoop:Disconnect(); staminaLoop = nil end
    if v then
        staminaLoop = RunService.RenderStepped:Connect(function()
            local ch = player.Character
            if ch and ch:FindFirstChild("Sprint") then
                local sprint = ch.Sprint
                if sprint:FindFirstChild("Overdrive") then sprint.Overdrive.Value = 1e9 end
                if sprint:FindFirstChild("Stamina") then sprint.Stamina.Value = 100 end
            end
        end)
    end
end

local function toggleAntiTemp(v)
    local ch = player.Character
    if ch then
        local temp = ch:FindFirstChild("Temperature")
        if temp then temp.Enabled = not v end
    end
end

local o2Loop = nil
local function toggleO2(v)
    if o2Loop then o2Loop:Disconnect(); o2Loop = nil end
    if v then
        o2Loop = RunService.RenderStepped:Connect(function()
            local ch = player.Character
            if ch then
                local breath = ch:FindFirstChild("Breath")
                if breath then
                    breath:SetAttribute("Max", 999999)
                    breath.Value = 999999
                end
                local blur = Lighting:FindFirstChild("Blur")
                if blur then blur.Enabled = false end
            end
        end)
    end
end

-- ============================================================
-- NIGHT 1
-- ============================================================
local function RefillFireplace()
    local char = player.Character or player.CharacterAdded:Wait()
    local OC = char.HumanoidRootPart.CFrame
    local wp = workspace:FindFirstChild("WoodPile")
    if wp then
        local d = wp:FindFirstChild("Detector")
        if d then d.CanCollide = false end
        char.HumanoidRootPart.CFrame = CFrame.new(-27.149, 8.7, -118.611)
        task.wait(0.25)
        local cl = d and d:FindFirstChild("ClickDetector")
       
