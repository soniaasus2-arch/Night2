

local player = game.Players.LocalPlayer
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local VirtualInputManager = game:GetService("VirtualInputManager")
local Players = game:GetService("Players")

-- ============================================================
-- SISTEMA DE GRAVAÇÃO
-- ============================================================

local Macro = {
    Gravando = false,
    Repetindo = false,
    Ações = {},
    TempoInicio = 0,
    UltimoTempo = 0,
    Velocidade = 1,
    Loop = false,
}

-- ============================================================
-- FUNÇÃO PARA GRAVAR AÇÕES
-- ============================================================

local function gravarAcao(tipo, dados)
    if not Macro.Gravando then return end
    
    local tempoAtual = tick() - Macro.TempoInicio
    local tempoDecorrido = tempoAtual - Macro.UltimoTempo
    
    table.insert(Macro.Ações, {
        tipo = tipo,
        dados = dados,
        tempo = tempoAtual,
        delay = tempoDecorrido
    })
    
    Macro.UltimoTempo = tempoAtual
end

-- ============================================================
-- GRAVAR TECLAS DO TECLADO
-- ============================================================

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if not Macro.Gravando then return end
    
    if input.KeyCode then
        gravarAcao("KeyDown", {
            KeyCode = input.KeyCode.Name,
            UserInputType = "Keyboard"
        })
    end
    
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        gravarAcao("MouseDown", {
            Button = "Left",
            Position = UserInputService:GetMouseLocation()
        })
    end
    
    if input.UserInputType == Enum.UserInputType.MouseButton2 then
        gravarAcao("MouseDown", {
            Button = "Right",
            Position = UserInputService:GetMouseLocation()
        })
    end
end)

UserInputService.InputEnded:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if not Macro.Gravando then return end
    
    if input.KeyCode then
        gravarAcao("KeyUp", {
            KeyCode = input.KeyCode.Name,
            UserInputType = "Keyboard"
        })
    end
    
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        gravarAcao("MouseUp", {
            Button = "Left",
            Position = UserInputService:GetMouseLocation()
        })
    end
    
    if input.UserInputType == Enum.UserInputType.MouseButton2 then
        gravarAcao("MouseUp", {
            Button = "Right",
            Position = UserInputService:GetMouseLocation()
        })
    end
end)

-- ============================================================
-- GRAVAR MOVIMENTO DO MOUSE
-- ============================================================

UserInputService.InputChanged:Connect(function(input)
    if not Macro.Gravando then return end
    
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        gravarAcao("MouseMove", {
            Position = input.Position,
            Delta = input.Delta
        })
    end
end)

-- ============================================================
-- GRAVAR MOVIMENTO DO PERSONAGEM (WASD)
-- ============================================================

local lastPosition = nil
local lastCFrame = nil

RunService.Heartbeat:Connect(function()
    if not Macro.Gravando then return end
    
    local char = player.Character
    if not char then return end
    
    local root = char:FindFirstChild("HumanoidRootPart")
    if not root then return end
    
    local pos = root.Position
    local cf = root.CFrame
    
    if lastPosition then
        if (pos - lastPosition).Magnitude > 0.5 then
            gravarAcao("MoveTo", {
                Position = pos,
                CFrame = cf
            })
            lastPosition = pos
            lastCFrame = cf
        end
    else
        lastPosition = pos
        lastCFrame = cf
        gravarAcao("MoveTo", {
            Position = pos,
            CFrame = cf
        })
    end
end)

-- ============================================================
-- REPETIR AÇÕES GRAVADAS
-- ============================================================

local repetindoLoop = nil

local function repetirAcoes()
    if repetindoLoop then
        repetindoLoop:Disconnect()
        repetindoLoop = nil
    end
    
    if #Macro.Ações == 0 then
        print("❌ Nenhuma ação gravada!")
        return
    end
    
    Macro.Repetindo = true
    print("▶️ Repetindo " .. #Macro.Ações .. " ações...")
    
    local indice = 1
    local tempoInicio = tick()
    
    repetindoLoop = RunService.Heartbeat:Connect(function()
        if not Macro.Repetindo then
            repetindoLoop:Disconnect()
            repetindoLoop = nil
            return
        end
        
        local tempoDecorrido = (tick() - tempoInicio) * Macro.Velocidade
        
        while indice <= #Macro.Ações and Macro.Ações[indice].tempo <= tempoDecorrido do
            local acao = Macro.Ações[indice]
            executarAcao(acao)
            indice = indice + 1
        end
        
        if indice > #Macro.Ações then
            if Macro.Loop then
                indice = 1
                tempoInicio = tick()
                print("🔄 Loop reiniciado!")
            else
                Macro.Repetindo = false
                repetindoLoop:Disconnect()
                repetindoLoop = nil
                print("⏹️ Repetição finalizada!")
            end
        end
    end)
end

-- ============================================================
-- EXECUTAR UMA AÇÃO
-- ============================================================

local function executarAcao(acao)
    local dados = acao.dados
    
    if acao.tipo == "KeyDown" then
        VirtualInputManager:SendKeyEvent(true, dados.KeyCode, false, game)
    elseif acao.tipo == "KeyUp" then
        VirtualInputManager:SendKeyEvent(false, dados.KeyCode, false, game)
    elseif acao.tipo == "MouseDown" then
        local mouseButton = dados.Button == "Left" and Enum.UserInputType.MouseButton1 or Enum.UserInputType.MouseButton2
        VirtualInputManager:SendMouseButtonEvent(dados.Position.X, dados.Position.Y, 0, true, mouseButton, false, game)
    elseif acao.tipo == "MouseUp" then
        local mouseButton = dados.Button == "Left" and Enum.UserInputType.MouseButton1 or Enum.UserInputType.MouseButton2
        VirtualInputManager:SendMouseButtonEvent(dados.Position.X, dados.Position.Y, 0, false, mouseButton, false, game)
    elseif acao.tipo == "MouseMove" then
        VirtualInputManager:SendMouseMovementEvent(dados.Position.X, dados.Position.Y, 0, false, game)
    elseif acao.tipo == "MoveTo" then
        local char = player.Character
        if char then
            local root = char:FindFirstChild("HumanoidRootPart")
            if root then
                root.CFrame = dados.CFrame
            end
        end
    end
end

-- ============================================================
-- FUNÇÕES DE CONTROLE
-- ============================================================

local function iniciarGravacao()
    if Macro.Gravando then
        print("⚠️ Já está gravando!")
        return
    end
    
    if Macro.Repetindo then
        pararRepeticao()
    end
    
    Macro.Ações = {}
    Macro.Gravando = true
    Macro.TempoInicio = tick()
    Macro.UltimoTempo = 0
    lastPosition = nil
    lastCFrame = nil
    
    print("🔴 GRAVAÇÃO INICIADA!")
    print("📌 Faça suas ações...")
end

local function pararGravacao()
    if not Macro.Gravando then
        print("⚠️ Não está gravando!")
        return
    end
    
    Macro.Gravando = false
    print("⏹️ GRAVAÇÃO PARADA!")
    print("📊 " .. #Macro.Ações .. " ações gravadas!")
end

local function iniciarRepeticao()
    if Macro.Repetindo then
        print("⚠️ Já está repetindo!")
        return
    end
    
    if #Macro.Ações == 0 then
        print("❌ Nenhuma ação gravada! Grave algo primeiro.")
        return
    end
    
    repetirAcoes()
end

local function pararRepeticao()
    if not Macro.Repetindo then
        print("⚠️ Não está repetindo!")
        return
    end
    
    Macro.Repetindo = false
    if repetindoLoop then
        repetindoLoop:Disconnect()
        repetindoLoop = nil
    end
    print("⏹️ Repetição parada!")
end

local function limparGravacao()
    if Macro.Gravando then
        pararGravacao()
    end
    if Macro.Repetindo then
        pararRepeticao()
    end
    Macro.Ações = {}
    print("🗑️ Ações limpas!")
end

-- ============================================================
-- CRIAR GUI DO MACRO
-- ============================================================

local function criarGUIMacro()
    -- Remover GUI antiga
    for _, v in pairs(player.PlayerGui:GetChildren()) do
        if v.Name == "MacroGUI" then
            v:Destroy()
        end
    end
    
    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "MacroGUI"
    screenGui.Parent = player.PlayerGui
    screenGui.ResetOnSpawn = false
    screenGui.IgnoreGuiInset = true
    
    -- Janela principal
    local mainFrame = Instance.new("Frame")
    mainFrame.Size = UDim2.new(0, 300, 0, 300)
    mainFrame.Position = UDim2.new(0.5, -150, 0.5, -150)
    mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 35)
    mainFrame.BackgroundTransparency = 0.08
    mainFrame.BorderSizePixel = 0
    mainFrame.ClipsDescendants = true
    mainFrame.Parent = screenGui
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 16)
    corner.Parent = mainFrame
    
    local border = Instance.new("UIStroke")
    border.Color = Color3.fromRGB(255, 140, 0)
    border.Thickness = 2
    border.Transparency = 0.3
    border.Parent = mainFrame
    
    -- Cabeçalho
    local header = Instance.new("Frame")
    header.Size = UDim2.new(1, 0, 0, 40)
    header.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
    header.BackgroundTransparency = 0.2
    header.BorderSizePixel = 0
    header.Parent = mainFrame
    
    local headerCorner = Instance.new("UICorner")
    headerCorner.CornerRadius = UDim.new(0, 16)
    headerCorner.Parent = header
    
    local title = Instance.new("TextLabel")
    title.Size = UDim2.new(1, -80, 1, 0)
    title.Position = UDim2.new(0, 10, 0, 0)
    title.Text = "🎬 MACRO RECORDER"
    title.TextColor3 = Color3.fromRGB(255, 255, 255)
    title.TextSize = 14
    title.Font = Enum.Font.GothamBold
    title.BackgroundTransparency = 1
    title.TextXAlignment = Enum.TextXAlignment.Left
    title.Parent = header
    
    -- Fechar
    local close = Instance.new("TextButton")
    close.Size = UDim2.new(0, 30, 0, 30)
    close.Position = UDim2.new(1, -38, 0, 5)
    close.Text = "X"
    close.TextSize = 18
    close.BackgroundColor3 = Color3.fromRGB(200, 60, 60)
    close.BackgroundTransparency = 0.3
    close.TextColor3 = Color3.fromRGB(255, 255, 255)
    close.Font = Enum.Font.GothamBold
    close.BorderSizePixel = 0
    close.Parent = header
    close.MouseButton1Click:Connect(function() screenGui:Destroy() end)
    
    -- Botões
    local function criarBotao(texto, y, cor, callback)
        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(0.9, 0, 0, 40)
        btn.Position = UDim2.new(0.05, 0, y, 0)
        btn.BackgroundColor3 = cor or Color3.fromRGB(45, 45, 60)
        btn.BackgroundTransparency = 0.2
        btn.BorderSizePixel = 0
        btn.Text = texto
        btn.TextColor3 = Color3.fromRGB(255, 255, 255)
        btn.TextSize = 14
        btn.Font = Enum.Font.GothamBold
        btn.Parent = mainFrame
        
        local btnCorner = Instance.new("UICorner")
        btnCorner.CornerRadius = UDim.new(0, 8)
        btnCorner.Parent = btn
        
        btn.MouseButton1Click:Connect(callback)
        return btn
    end
    
    local statusLabel = Instance.new("TextLabel")
    statusLabel.Size = UDim2.new(0.9, 0, 0, 20)
    statusLabel.Position = UDim2.new(0.05, 0, 0.13, 0)
    statusLabel.BackgroundTransparency = 1
    statusLabel.Text = "⏸️ Parado"
    statusLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    statusLabel.TextSize = 12
    statusLabel.Font = Enum.Font.Gotham
    statusLabel.Parent = mainFrame
    
    -- Botão Gravar
    local gravarBtn = criarBotao("🔴 GRAVAR", 0.20, Color3.fromRGB(200, 50, 50), function()
        if Macro.Gravando then
            pararGravacao()
            gravarBtn.Text = "🔴 GRAVAR"
            gravarBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
            statusLabel.Text = "⏸️ Parado (" .. #Macro.Ações .. " ações)"
            statusLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
        else
            iniciarGravacao()
            gravarBtn.Text = "⏹️ PARAR"
            gravarBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
            statusLabel.Text = "🔴 Gravando..."
            statusLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
        end
    end)
    
    -- Botão Repetir
    local repetirBtn = criarBotao("▶️ REPETIR", 0.35, Color3.fromRGB(0, 200, 100), function()
        if Macro.Repetindo then
            pararRepeticao()
            repetirBtn.Text = "▶️ REPETIR"
            repetirBtn.BackgroundColor3 = Color3.fromRGB(0, 200, 100)
            statusLabel.Text = "⏸️ Parado (" .. #Macro.Ações .. " ações)"
            statusLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
        else
            iniciarRepeticao()
            repetirBtn.Text = "⏹️ PARAR REPETIÇÃO"
            repetirBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
            statusLabel.Text = "▶️ Repetindo (" .. #Macro.Ações .. " ações)"
            statusLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
        end
    end)
    
    -- Botão Loop
    local loopBtn = criarBotao("🔁 LOOP: OFF", 0.50, Color3.fromRGB(80, 80, 100), function()
        Macro.Loop = not Macro.Loop
        loopBtn.Text = Macro.Loop and "🔁 LOOP: ON" or "🔁 LOOP: OFF"
        loopBtn.BackgroundColor3 = Macro.Loop and Color3.fromRGB(0, 200, 0) or Color3.fromRGB(80, 80, 100)
    end)
    
    -- Botão Limpar
    local limparBtn = criarBotao("🗑️ LIMPAR", 0.65, Color3.fromRGB(200, 100, 0), function()
        limparGravacao()
        statusLabel.Text = "⏸️ Parado (0 ações)"
        statusLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
        gravarBtn.Text = "🔴 GRAVAR"
        gravarBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
        repetirBtn.Text = "▶️ REPETIR"
        repetirBtn.BackgroundColor3 = Color3.fromRGB(0, 200, 100)
    end)
    
    -- Slider de velocidade
    local velFrame = Instance.new("Frame")
    velFrame.Size = UDim2.new(0.9, 0, 0, 30)
    velFrame.Position = UDim2.new(0.05, 0, 0.80, 0)
    velFrame.BackgroundTransparency = 1
    velFrame.Parent = mainFrame
    
    local velLabel = Instance.new("TextLabel")
    velLabel.Size = UDim2.new(0.4, 0, 1, 0)
    velLabel.BackgroundTransparency = 1
    velLabel.Text = "⚡ Velocidade: 1x"
    velLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    velLabel.TextSize = 12
    velLabel.Font = Enum.Font.Gotham
    velLabel.TextXAlignment = Enum.TextXAlignment.Left
    velLabel.Parent = velFrame
    
    local velSlider = Instance.new("TextButton")
    velSlider.Size = UDim2.new(0.5, 0, 0.8, 0)
    velSlider.Position = UDim2.new(0.45, 0, 0.1, 0)
    velSlider.BackgroundColor3 = Color3.fromRGB(60, 60, 80)
    velSlider.BorderSizePixel = 0
    velSlider.Text = "1x"
    velSlider.TextColor3 = Color3.fromRGB(255, 255, 255)
    velSlider.TextSize = 12
    velSlider.Font = Enum.Font.GothamBold
    velSlider.Parent = velFrame
    
    local velCorner = Instance.new("UICorner")
    velCorner.CornerRadius = UDim.new(0, 6)
    velCorner.Parent = velSlider
    
    local velocidades = {0.5, 0.75, 1, 1.25, 1.5, 2, 3}
    local velIndex = 3
    
    velSlider.MouseButton1Click:Connect(function()
        velIndex = velIndex + 1
        if velIndex > #velocidades then velIndex = 1 end
        Macro.Velocidade = velocidades[velIndex]
        velSlider.Text = Macro.Velocidade .. "x"
        velLabel.Text = "⚡ Velocidade: " .. Macro.Velocidade .. "x"
    end)
    
    return screenGui
end

-- ============================================================
-- COMANDOS
-- ============================================================

_G.Macro = {
    Gravar = iniciarGravacao,
    Parar = pararGravacao,
    Repetir = iniciarRepeticao,
    PararRepetir = pararRepeticao,
    Limpar = limparGravacao,
    Velocidade = function(v) Macro.Velocidade = v end,
    Loop = function(v) Macro.Loop = v end,
}

-- ============================================================
-- INICIALIZAR
-- ============================================================

criarGUIMacro()

print("")
print("═══════════════════════════════════════════")
print("🎬 MACRO RECORDER CARREGADO!")
print("═══════════════════════════════════════════")
print("📌 Como usar:")
print("   1. Clique em GRAVAR")
print("   2. Faça suas ações no jogo")
print("   3. Clique em PARAR")
print("   4. Clique em REPETIR")
print("   5. O script vai repetir tudo!")
print("═══════════════════════════════════════════")
