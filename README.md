
local player = game.Players.LocalPlayer
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local Camera = workspace.CurrentCamera
local UserInputService = game:GetService("UserInputService")
local RS = game.ReplicatedStorage
local HttpService = game:GetService("HttpService")
-- Verifica se o jogador atual é o criador
local isDeveloper = (player.Name == "daviroblox2023r")

if is Developer then
    -- Se for o criador, pula o sistema de key
    print("👑 Desenvolvedor detectado! Pulando sistema de key...")
    keyValidada = true
    player:SetAttribute("DAVI_KEY", "DEV_ACCESS")
    print("✅ DAVI HUB iniciado em modo desenvolvedor!")
    return  -- 👈 O SCRIPT PARA AQUI
end

-- Se chegou aqui, é porque NÃO é o desenvolvedor
if isAtivado() then
    print("✅ DAVI HUB já está ativado! Continuando...")
else
    print("🔑 Aguardando ativação da key...")
    criarGUIAtivacao()

    while not keyValidada do
        task.wait(0.5)
        local guiExists = false
        for _, v in pairs(player.PlayerGui:GetChildren()) do
            if v.Name == "KeySystem" or v.Name == "AvisarGUI" then
                guiExists = true
                break
            end
        end
        if not guiExists and not keyValidada then
            print("❌ Ativação cancelada.")
            return
        end
    end
end
local function isAtivado()
    local keySalva = player:GetAttribute("DAVI_KEY")
    if keySalva then
        for _, item in pairs(KEYS_SEQUENCIA) do
            if item.key == keySalva and not item.usada then
                item.usada = true
                return true
            end
        end
    end
    return false
end



local WEBHOOK_URL = "https://discord.com/api/webhooks/1524546983799427194/WyTosfrV6Opc1MPOpmTJmYNlCzBu0gpRSJ89dUnqcNVYbqJ373-tCfTLUMBgOTidUEh3"

local KEYS_SEQUENCIA = {
    {key = "free_10182alapapqaoqkfa", link = "https://link-target.net/5450045/U8UGMFUQ22Uc", usada = false, posicao = 1},
    {key = "free_20394blbqbqbrbrlsb", link = "https://direct-link.net/5450045/QykQLu41Fp2x", usada = false, posicao = 2},
    {key = "free_30567cmcrcrcscsmtc", link = "https://direct-link.net/5450045/m1aASokV7pIF", usada = false, posicao = 3},
    {key = "free_40821dndsdtdudunud", link = "https://direct-link.net/5450045/glVLfwhKt4et", usada = false, posicao = 4},
    {key = "free_50943eoeuevevevove", link = "https://link-center.net/5450045/l1peI74weA4g", usada = false, posicao = 5},
    {key = "free_61054fpfwfwfwfwpwf", link = "https://direct-link.net/5450045/eaCiSMyybd74", usada = false, posicao = 6},
    {key = "free_71265gqgxgxgxgxqgx", link = "https://link-hub.net/5450045/TGIbYBsV7EcU", usada = false, posicao = 7},
    {key = "free_81376hrhyhyhyhyrhy", link = "https://link-hub.net/5450045/IkJsx7RSCwyp", usada = false, posicao = 8},
    {key = "free_91487isizizizizsiz", link = "https://link-center.net/5450045/YR4NQ7ewNSkJ", usada = false, posicao = 9},
    {key = "free_101598jtjajajajataj", link = "https://link-target.net/5450045/gdChsmYq0rb5", usada = false, posicao = 10},
}

local keyValidada = false

-- FUNÇÕES DO SISTEMA DE KEY
local function verificarKey(key)
    for _, item in pairs(KEYS_SEQUENCIA) do
        if item.key == key then
            if item.usada then return false, "❌ KEY JÁ UTILIZADA!" end
            return true, "✅ KEY VÁLIDA!"
        end
    end
    return false, "❌ KEY INVÁLIDA!"
end

local function marcarKeyUsada(key)
    for _, item in pairs(KEYS_SEQUENCIA) do
        if item.key == key then
            item.usada = true
            for _, prox in pairs(KEYS_SEQUENCIA) do
                if not prox.usada then return prox.link end
            end
            return nil
        end
    end
    return nil
end

local function todasKeysAcabaram()
    for _, item in pairs(KEYS_SEQUENCIA) do
        if not item.usada then return false end
    end
    return true
end

local function avisarCriador(comPing)
    local content = comPing and "@here 🔥 **TODAS AS KEYS DO DAVI HUB ACABARAM!**" or ""
    local data = {
        ["content"] = content,
        ["embeds"] = {{
            ["title"] = "🚨 KEYS ESGOTADAS - DAVI HUB",
            ["color"] = 16711680,
            ["fields"] = {
                {["name"] = "📊 Situação", ["value"] = "Todas as **10 keys** foram distribuídas!", ["inline"] = false},
                {["name"] = "👤 Solicitante", ["value"] = player.Name .. " (ID: " .. player.UserId .. ")", ["inline"] = true},
                {["name"] = "📅 Data/Hora", ["value"] = os.date("%d/%m/%Y %H:%M:%S"), ["inline"] = true},
                {["name"] = "🔔 Tipo de Alerta", ["value"] = comPing and "⚡ COM @here" or "🔕 SEM PING", ["inline"] = true},
                {["name"] = "🌐 Servidor", ["value"] = game.JobId or "N/A", ["inline"] = true},
                {["name"] = "💡 Ação Necessária", ["value"] = "Criar novas keys e atualizar o sistema.", ["inline"] = false}
            },
            ["footer"] = {["text"] = "DAVI HUB - Sistema de Distribuição de Keys"},
            ["timestamp"] = os.date("!%Y-%m-%dT%H:%M:%S.000Z")
        }}
    }
    local json = HttpService:JSONEncode(data)
    pcall(function()
        local request = syn and syn.request or request or http_request
        if request then
            request({Url = WEBHOOK_URL, Method = "POST", Headers = {["Content-Type"] = "application/json"}, Body = json})
        end
    end)
end
-- GUI DE AVISO (QUANDO KEYS ACABAM)
local function criarGUIAvisar()
    for _, v in pairs(player.PlayerGui:GetChildren()) do if v.Name == "AvisarGUI" then v:Destroy() end end
    local gui = Instance.new("ScreenGui")
    gui.Name = "AvisarGUI"
    gui.Parent = player.PlayerGui
    gui.ResetOnSpawn = false
    gui.IgnoreGuiInset = true

    local fundo = Instance.new("Frame")
    fundo.Size = UDim2.new(1, 0, 1, 0)
    fundo.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    fundo.BackgroundTransparency = 0.6
    fundo.Parent = gui

    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 450, 0, 280)
    frame.Position = UDim2.new(0.5, -225, 0.5, -140)
    frame.BackgroundColor3 = Color3.fromRGB(20, 20, 35)
    frame.BackgroundTransparency = 0.05
    frame.BorderSizePixel = 0
    frame.ClipsDescendants = true
    frame.Parent = fundo

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 16)
    corner.Parent = frame

    local border = Instance.new("UIStroke")
    border.Color = Color3.fromRGB(255, 140, 0)
    border.Thickness = 2
    border.Transparency = 0.3
    border.Parent = frame

    -- Cabeçalho
    local header = Instance.new("Frame")
    header.Size = UDim2.new(1, 0, 0, 45)
    header.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
    header.BackgroundTransparency = 0.2
    header.BorderSizePixel = 0
    header.Parent = frame
    local headerCorner = Instance.new("UICorner")
    headerCorner.CornerRadius = UDim.new(0, 16)
    headerCorner.Parent = header

    local title = Instance.new("TextLabel")
    title.Size = UDim2.new(1, -60, 1, 0)
    title.Position = UDim2.new(0, 15, 0, 0)
    title.Text = "🚨 KEYS ESGOTADAS!"
    title.TextColor3 = Color3.fromRGB(255, 255, 255)
    title.TextSize = 18
    title.Font = Enum.Font.GothamBold
    title.BackgroundTransparency = 1
    title.TextXAlignment = Enum.TextXAlignment.Left
    title.Parent = header

    local closeBtn = Instance.new("TextButton")
    closeBtn.Size = UDim2.new(0, 30, 0, 30)
    closeBtn.Position = UDim2.new(1, -38, 0, 7.5)
    closeBtn.Text = "✕"
    closeBtn.TextSize = 18
    closeBtn.BackgroundColor3 = Color3.fromRGB(200, 60, 60)
    closeBtn.BackgroundTransparency = 0.3
    closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    closeBtn.Font = Enum.Font.GothamBold
    closeBtn.BorderSizePixel = 0
    closeBtn.Parent = header
    local closeCorner = Instance.new("UICorner")
    closeCorner.CornerRadius = UDim.new(0, 8)
    closeCorner.Parent = closeBtn
    closeBtn.MouseButton1Click:Connect(function()
        gui:Destroy()
        print("❌ Ativação cancelada.")
    end)

    -- Conteúdo
    local sub = Instance.new("TextLabel")
    sub.Size = UDim2.new(1, 0, 0, 25)
    sub.Position = UDim2.new(0, 0, 0.2, 0)
    sub.Text = "Todas as 10 keys foram distribuídas!"
    sub.TextColor3 = Color3.fromRGB(255, 200, 200)
    sub.TextSize = 14
    sub.Font = Enum.Font.Gotham
    sub.BackgroundTransparency = 1
    sub.Parent = frame

    local instrucao = Instance.new("TextLabel")
    instrucao.Size = UDim2.new(1, 0, 0, 25)
    instrucao.Position = UDim2.new(0, 0, 0.32, 0)
    instrucao.Text = "Deseja avisar o criador para fazer novas keys?"
    instrucao.TextColor3 = Color3.fromRGB(200, 200, 200)
    instrucao.TextSize = 14
    instrucao.Font = Enum.Font.Gotham
    instrucao.BackgroundTransparency = 1
    instrucao.Parent = frame

    local btnPing = Instance.new("TextButton")
    btnPing.Size = UDim2.new(0.4, 0, 0, 45)
    btnPing.Position = UDim2.new(0.05, 0, 0.5, 0)
    btnPing.Text = "🔔 COM @here"
    btnPing.TextColor3 = Color3.fromRGB(255, 255, 255)
    btnPing.TextSize = 16
    btnPing.Font = Enum.Font.GothamBold
    btnPing.BackgroundColor3 = Color3.fromRGB(255, 100, 0)
    btnPing.BackgroundTransparency = 0.15
    btnPing.BorderSizePixel = 0
    btnPing.Parent = frame
    local btnPingCorner = Instance.new("UICorner")
    btnPingCorner.CornerRadius = UDim.new(0, 10)
    btnPingCorner.Parent = btnPing

    local btnSemPing = Instance.new("TextButton")
    btnSemPing.Size = UDim2.new(0.4, 0, 0, 45)
    btnSemPing.Position = UDim2.new(0.55, 0, 0.5, 0)
    btnSemPing.Text = "🔕 SEM PING"
    btnSemPing.TextColor3 = Color3.fromRGB(255, 255, 255)
    btnSemPing.TextSize = 16
    btnSemPing.Font = Enum.Font.GothamBold
    btnSemPing.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
    btnSemPing.BackgroundTransparency = 0.15
    btnSemPing.BorderSizePixel = 0
    btnSemPing.Parent = frame
    local btnSemPingCorner = Instance.new("UICorner")
    btnSemPingCorner.CornerRadius = UDim.new(0, 10)
    btnSemPingCorner.Parent = btnSemPing

    local status = Instance.new("TextLabel")
    status.Size = UDim2.new(1, 0, 0, 25)
    status.Position = UDim2.new(0, 0, 0.75, 0)
    status.Text = "💡 Escolha como deseja avisar o criador"
    status.TextColor3 = Color3.fromRGB(150, 150, 150)
    status.TextSize = 13
    status.Font = Enum.Font.Gotham
    status.BackgroundTransparency = 1
    status.Parent = frame

    btnPing.MouseButton1Click:Connect(function()
        status.Text = "⏳ Enviando..."
        status.TextColor3 = Color3.fromRGB(255, 255, 100)
        btnPing.Visible = false
        btnSemPing.Visible = false
        avisarCriador(true)
        status.Text = "✅ Aviso enviado! Criador será notificado."
        status.TextColor3 = Color3.fromRGB(100, 255, 100)
        btnPing.Text = "✅ ENVIADO!"
    end)

    btnSemPing.MouseButton1Click:Connect(function()
        status.Text = "⏳ Enviando..."
        status.TextColor3 = Color3.fromRGB(255, 255, 100)
        btnPing.Visible = false
        btnSemPing.Visible = false
        avisarCriador(false)
        status.Text = "✅ Aviso enviado! Criador será notificado."
        status.TextColor3 = Color3.fromRGB(100, 255, 100)
        btnSemPing.Text = "✅ ENVIADO!"
    end)

    return gui
end

-- GUI DE ATIVAÇÃO
local function criarGUIAtivacao()
    for _, v in pairs(player.PlayerGui:GetChildren()) do
        if v.Name == "KeySystem" then v:Destroy() end
    end

    if todasKeysAcabaram() then
        criarGUIAvisar()
        return
    end

    local gui = Instance.new("ScreenGui")
    gui.Name = "KeySystem"
    gui.Parent = player.PlayerGui
    gui.ResetOnSpawn = false
    gui.IgnoreGuiInset = true

    local fundo = Instance.new("Frame")
    fundo.Size = UDim2.new(1, 0, 1, 0)
    fundo.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    fundo.BackgroundTransparency = 0.6
    fundo.Parent = gui

    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 450, 0, 400)
    frame.Position = UDim2.new(0.5, -225, 0.5, -200)
    frame.BackgroundColor3 = Color3.fromRGB(20, 20, 35)
    frame.BackgroundTransparency = 0.05
    frame.BorderSizePixel = 0
    frame.ClipsDescendants = true
    frame.Parent = fundo

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 16)
    corner.Parent = frame

    local border = Instance.new("UIStroke")
    border.Color = Color3.fromRGB(255, 140, 0)
    border.Thickness = 2
    border.Transparency = 0.3
    border.Parent = frame

   -- Cabeçalho
    local header = Instance.new("Frame")
    header.Size = UDim2.new(1, 0, 0, 45)
    header.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
    header.BackgroundTransparency = 0.2
    header.BorderSizePixel = 0
    header.Parent = frame
    local headerCorner = Instance.new("UICorner")
    headerCorner.CornerRadius = UDim.new(0, 16)
    headerCorner.Parent = header

    local title = Instance.new("TextLabel")
    title.Size = UDim2.new(1, -80, 1, 0)
    title.Position = UDim2.new(0, 15, 0, 0)
    title.Text = "🔑 DISTRIBUIÇÃO DE KEYS"
    title.TextColor3 = Color3.fromRGB(255, 255, 255)
    title.TextSize = 18
    title.Font = Enum.Font.GothamBold
    title.BackgroundTransparency = 1
    title.TextXAlignment = Enum.TextXAlignment.Left
    title.Parent = header

    -- Minimizar
    local minBtn = Instance.new("TextButton")
    minBtn.Size = UDim2.new(0, 30, 0, 30)
    minBtn.Position = UDim2.new(1, -70, 0, 7.5)
    minBtn.Text = "−"
    minBtn.TextSize = 22
    minBtn.BackgroundColor3 = Color3.fromRGB(80, 80, 100)
    minBtn.BackgroundTransparency = 0.4
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
        frame.Size = minimized and UDim2.new(0, 450, 0, 45) or UDim2.new(0, 450, 0, 400)
        minBtn.Text = minimized and "+" or "−"
        for _, child in pairs(frame:GetChildren()) do
            if child ~= header then
                child.Visible = not minimized
            end
        end
    end)

    -- Fechar
    local closeBtn = Instance.new("TextButton")
    closeBtn.Size = UDim2.new(0, 30, 0, 30)
    closeBtn.Position = UDim2.new(1, -38, 0, 7.5)
    closeBtn.Text = "✕"
    closeBtn.TextSize = 18
    closeBtn.BackgroundColor3 = Color3.fromRGB(200, 60, 60)
    closeBtn.BackgroundTransparency = 0.3
    closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    closeBtn.Font = Enum.Font.GothamBold
    closeBtn.BorderSizePixel = 0
    closeBtn.Parent = header
    local closeCorner = Instance.new("UICorner")
    closeCorner.CornerRadius = UDim.new(0, 8)
    closeCorner.Parent = closeBtn
    closeBtn.MouseButton1Click:Connect(function()
        gui:Destroy()
        print("❌ Ativação cancelada.")
    end)

    -- Conteúdo
    local conteudo = Instance.new("Frame")
    conteudo.Name = "Conteudo"
    conteudo.Size = UDim2.new(1, 0, 1, -45)
    conteudo.Position = UDim2.new(0, 0, 0, 45)
    conteudo.BackgroundTransparency = 1
    conteudo.Parent = frame

    local keysUsadas = 0
    for _, item in pairs(KEYS_SEQUENCIA) do if item.usada then keysUsadas = keysUsadas + 1 end end
    local totalKeys = #KEYS_SEQUENCIA

    local sub = Instance.new("TextLabel")
    sub.Size = UDim2.new(1, 0, 0, 25)
    sub.Position = UDim2.new(0, 0, 0.05, 0)
    sub.Text = "Digite sua key de ativação  |  " .. keysUsadas .. "/" .. totalKeys .. " keys distribuídas"
    sub.TextColor3 = Color3.fromRGB(200, 200, 200)
    sub.TextSize = 13
    sub.Font = Enum.Font.Gotham
    sub.BackgroundTransparency = 1
    sub.Parent = conteudo

    local keyBox = Instance.new("TextBox")
    keyBox.Size = UDim2.new(0.8, 0, 0, 45)
    keyBox.Position = UDim2.new(0.1, 0, 0.15, 0)
    keyBox.BackgroundColor3 = Color3.fromRGB(35, 35, 50)
    keyBox.BackgroundTransparency = 0.2
    keyBox.TextColor3 = Color3.fromRGB(255, 255, 255)
    keyBox.TextSize = 18
    keyBox.Font = Enum.Font.GothamBold
    keyBox.Text = ""
    keyBox.PlaceholderText = "Cole sua key aqui..."
    keyBox.ClearTextOnFocus = true
    keyBox.BorderSizePixel = 0
    keyBox.Parent = conteudo
    local keyCorner = Instance.new("UICorner")
    keyCorner.CornerRadius = UDim.new(0, 10)
    keyCorner.Parent = keyBox

    local btnAtivar = Instance.new("TextButton")
    btnAtivar.Size = UDim2.new(0.35, 0, 0, 45)
    btnAtivar.Position = UDim2.new(0.1, 0, 0.30, 0)
    btnAtivar.Text = "🔓 ATIVAR"
    btnAtivar.TextColor3 = Color3.fromRGB(255, 255, 255)
    btnAtivar.TextSize = 18
    btnAtivar.Font = Enum.Font.GothamBold
    btnAtivar.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
    btnAtivar.BackgroundTransparency = 0.15
    btnAtivar.BorderSizePixel = 0
    btnAtivar.Parent = conteudo
    local btnCorner = Instance.new("UICorner")
    btnCorner.CornerRadius = UDim.new(0, 10)
    btnCorner.Parent = btnAtivar

    -- Botão Get Key
    local btnGetKey = Instance.new("TextButton")
    btnGetKey.Size = UDim2.new(0.35, 0, 0, 45)
    btnGetKey.Position = UDim2.new(0.55, 0, 0.30, 0)
    btnGetKey.Text = "🔗 OBTER KEY"
    btnGetKey.TextColor3 = Color3.fromRGB(255, 255, 255)
    btnGetKey.TextSize = 16
    btnGetKey.Font = Enum.Font.GothamBold
    btnGetKey.BackgroundColor3 = Color3.fromRGB(45, 150, 200)
    btnGetKey.BackgroundTransparency = 0.15
    btnGetKey.BorderSizePixel = 0
    btnGetKey.Parent = conteudo
    local btnGetKeyCorner = Instance.new("UICorner")
    btnGetKeyCorner.CornerRadius = UDim.new(0, 10)
    btnGetKeyCorner.Parent = btnGetKey

    btnGetKey.MouseButton1Click:Connect(function()
        local proxKey = nil
        for _, item in pairs(KEYS_SEQUENCIA) do
            if not item.usada then
                proxKey = item
                break
            end
        end
        if proxKey then
            local link = proxKey.link
            local sucesso = pcall(function()
                setclipboard(link)
            end)
            if sucesso then
                status.Text = "✅ Link copiado! Compartilhe com alguém."
                status.TextColor3 = Color3.fromRGB(100, 255, 100)
            else
                status.Text = "📋 Link: " .. link
                status.TextColor3 = Color3.fromRGB(255, 200, 100)
            end
        else
            status.Text = "❌ Nenhuma key disponível!"
            status.TextColor3 = Color3.fromRGB(255, 100, 100)
        end
    end)

    -- Status
    local status = Instance.new("TextLabel")
    status.Size = UDim2.new(1, 0, 0, 25)
    status.Position = UDim2.new(0, 0, 0.42, 0)
    status.Text = "💡 Digite sua key e clique em ATIVAR"
    status.TextColor3 = Color3.fromRGB(150, 150, 150)
    status.TextSize = 13
    status.Font = Enum.Font.Gotham
    status.BackgroundTransparency = 1
    status.Parent = conteudo

    local linkFrame = Instance.new("Frame")
    linkFrame.Size = UDim2.new(0.9, 0, 0, 60)
    linkFrame.Position = UDim2.new(0.05, 0, 0.50, 0)
    linkFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 45)
    linkFrame.BackgroundTransparency = 0.2
    linkFrame.BorderSizePixel = 0
    linkFrame.Visible = false
    linkFrame.Parent = conteudo
    local linkCorner = Instance.new("UICorner")
    linkCorner.CornerRadius = UDim.new(0, 8)
    linkCorner.Parent = linkFrame

    local linkLabel = Instance.new("TextLabel")
    linkLabel.Size = UDim2.new(1, 0, 0, 20)
    linkLabel.Position = UDim2.new(0, 0, 0.05, 0)
    linkLabel.Text = "🔗 PRÓXIMA KEY DISPONÍVEL:"
    linkLabel.TextColor3 = Color3.fromRGB(255, 200, 50)
    linkLabel.TextSize = 13
    linkLabel.Font = Enum.Font.GothamBold
    linkLabel.BackgroundTransparency = 1
    linkLabel.Parent = linkFrame

    local linkTexto = Instance.new("TextLabel")
    linkTexto.Size = UDim2.new(1, 0, 0, 25)
    linkTexto.Position = UDim2.new(0, 0, 0.35, 0)
    linkTexto.Text = ""
    linkTexto.TextColor3 = Color3.fromRGB(100, 200, 255)
    linkTexto.TextSize = 13
    linkTexto.Font = Enum.Font.Gotham
    linkTexto.BackgroundTransparency = 1
    linkTexto.Parent = linkFrame

    local linkInstrucao = Instance.new("TextLabel")
    linkInstrucao.Size = UDim2.new(1, 0, 0, 15)
    linkInstrucao.Position = UDim2.new(0, 0, 0.70, 0)
    linkInstrucao.Text = "💡 Compartilhe este link com a próxima pessoa!"
    linkInstrucao.TextColor3 = Color3.fromRGB(150, 150, 150)
    linkInstrucao.TextSize = 11
    linkInstrucao.Font = Enum.Font.Gotham
    linkInstrucao.BackgroundTransparency = 1
    linkInstrucao.Parent = linkFrame

    local btnCopiar = Instance.new("TextButton")
    btnCopiar.Size = UDim2.new(0.3, 0, 0, 25)
    btnCopiar.Position = UDim2.new(0.6, 0, 0.35, 0)
    btnCopiar.Text = "📋 COPIAR LINK"
    btnCopiar.TextColor3 = Color3.fromRGB(255, 255, 255)
    btnCopiar.TextSize = 12
    btnCopiar.Font = Enum.Font.GothamBold
    btnCopiar.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
    btnCopiar.BackgroundTransparency = 0.2
    btnCopiar.BorderSizePixel = 0
    btnCopiar.Visible = false
    btnCopiar.Parent = linkFrame
    local btnCopiarCorner = Instance.new("UICorner")
    btnCopiarCorner.CornerRadius = UDim.new(0, 6)
    btnCopiarCorner.Parent = btnCopiar
    btnCopiar.MouseButton1Click:Connect(function()
        if linkTexto.Text ~= "" then
            setclipboard(linkTexto.Text)
            btnCopiar.Text = "✅ COPIADO!"
            task.wait(1)
            btnCopiar.Text = "📋 COPIAR LINK"
        end
    end)

    btnAtivar.MouseButton1Click:Connect(function()
        local key = keyBox.Text
        if key == "" then
            status.Text = "❌ Digite uma key!"
            status.TextColor3 = Color3.fromRGB(255, 100, 100)
            return
        end

        local valida, mensagem = verificarKey(key)

        if valida then
            status.Text = "✅ KEY VÁLIDA! Ativando DAVI HUB..."
            status.TextColor3 = Color3.fromRGB(100, 255, 100)
            btnAtivar.Text = "✅ ATIVADO!"
            btnAtivar.BackgroundColor3 = Color3.fromRGB(0, 200, 0)

            local proximoLink = marcarKeyUsada(key)
            player:SetAttribute("DAVI_KEY", key)
            keyValidada = true

            if proximoLink then
                linkFrame.Visible = true
                linkTexto.Text = proximoLink
                btnCopiar.Visible = true
                print("🔗 PRÓXIMA KEY: " .. proximoLink)
            else
                status.Text = "🎉 Todas as keys foram distribuídas!"
                status.TextColor3 = Color3.fromRGB(255, 200, 50)
                linkFrame.Visible = true
                linkTexto.Text = "❌ Nenhuma key disponível!"
                linkTexto.TextColor3 = Color3.fromRGB(255, 100, 100)
                btnCopiar.Visible = false
                print("🎉 TODAS AS KEYS FORAM DISTRIBUÍDAS!")
            end
            task.wait(1.5)
            gui:Destroy()

            if todasKeysAcabaram() then
                criarGUIAvisar()
            else
                print("✅ DAVI HUB ATIVADO COM SUCESSO!")
            end
        else
            status.Text = mensagem
            status.TextColor3 = Color3.fromRGB(255, 100, 100)
            keyBox.Text = ""
        end
    end)

    keyBox.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            btnAtivar.MouseButton1Click:Fire()
        end
    end)

    keyBox.FocusLost:Connect(function()
        local key = keyBox.Text
        if key ~= "" then
            local valida, mensagem = verificarKey(key)
            status.Text = valida and "✅ KEY VÁLIDA! Clique em ATIVAR." or mensagem
            status.TextColor3 = valida and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
        end
    end)

    return gui
end
-- ============================================================
-- 🔒 BLOQUEIA O SCRIPT ATÉ A KEY SER VALIDADA
-- ============================================================

local function isAtivado()
    local keySalva = player:GetAttribute("DAVI_KEY")
    if keySalva then
        for _, item in pairs(KEYS_SEQUENCIA) do
            if item.key == keySalva and not item.usada then
                item.usada = true
                return true
            end
        end
    end
    return false
end

if isAtivado() then
    print("✅ DAVI HUB já está ativado! Continuando...")
else
    print("🔑 Aguardando ativação da key...")
    criarGUIAtivacao()

    while not keyValidada do
        task.wait(0.5)
        local guiExists = false
        for _, v in pairs(player.PlayerGui:GetChildren()) do
            if v.Name == "KeySystem" or v.Name == "AvisarGUI" then
                guiExists = true
                break
            end
        end
        if not guiExists and not keyValidada then
            print("❌ Ativação cancelada.")
            return
        end
    end
end


-- ============================================================
-- 👆 SISTEMA DE KEY TERMINA AQUI
-- ============================================================

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
-- BOTÃO EMBAIXO DA GUI (ALÇA PARA ARRASTAR)
-- ============================================================
local moverBtn = Instance.new("TextButton")
moverBtn.Name = "MoverBtn"
moverBtn.Size = UDim2.new(0.4, 0, 0, 25)
moverBtn.Position = UDim2.new(0.3, 0, 1, -10)
moverBtn.BackgroundColor3 = Color3.fromRGB(180, 180, 180)
moverBtn.BackgroundTransparency = 0.3
moverBtn.Text = "≡≡≡"
moverBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
moverBtn.TextSize = 14
moverBtn.Font = Enum.Font.GothamBold
moverBtn.BorderSizePixel = 0
moverBtn.ZIndex = 10
moverBtn.Parent = mainFrame

local btnCorner = Instance.new("UICorner")
btnCorner.CornerRadius = UDim.new(0, 10)
btnCorner.Parent = moverBtn

moverBtn.InputBegan:Connect(function(input)
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

moverBtn.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        dragInput = input
    end
end)
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
-- ARRASTAR (CABEÇALHO)
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
        if cl then fireclickdetector(cl) end
        task.wait(0.25)
        char.HumanoidRootPart.CFrame = CFrame.new(-45.113, 7.849, -60.241)
        task.wait(0.5)
        char.HumanoidRootPart.CFrame = OC
    end
end

local function GrabWood()
    local char = player.Character or player.CharacterAdded:Wait()
    local OC = char.HumanoidRootPart.CFrame
    local wp = workspace:FindFirstChild("WoodPile")
    if wp then
        local d = wp:FindFirstChild("Detector")
        if d then d.CanCollide = false end
        char.HumanoidRootPart.CFrame = CFrame.new(-27.149, 8.7, -118.611)
        task.wait(0.25)
        local cl = d and d:FindFirstChild("ClickDetector")
        if cl then fireclickdetector(cl) end
        task.wait(0.25)
        char.HumanoidRootPart.CFrame = OC
    end
end

local function RefillGenerator()
    local char = player.Character or player.CharacterAdded:Wait()
    local OC = char.HumanoidRootPart.CFrame
    char.HumanoidRootPart.CFrame = CFrame.new(-76.039, 4.675, -133.78)
    task.wait(0.25)
    local shack = workspace:FindFirstChild("Shack")
    if shack then
        local jc = shack:FindFirstChild("JerryCan")
        if jc then
            local cl = jc:FindFirstChild("ClickDetector")
            if cl then fireclickdetector(cl) end
        end
        task.wait(0.5)
        local gen = shack:FindFirstChild("Generator")
        if gen then
            local cl = gen:FindFirstChild("ClickDetector")
            if cl then fireclickdetector(cl) end
        end
    end
    task.wait(0.5)
    char.HumanoidRootPart.CFrame = OC
end

local function GrabJerryCan()
    local char = player.Character or player.CharacterAdded:Wait()
    local OC = char.HumanoidRootPart.CFrame
    char.HumanoidRootPart.CFrame = CFrame.new(-76.039, 4.675, -133.78)
    task.wait(0.25)
    local shack = workspace:FindFirstChild("Shack")
    if shack then
        local jc = shack:FindFirstChild("JerryCan")
        if jc then
            local cl = jc:FindFirstChild("ClickDetector")
            if cl then fireclickdetector(cl) end
        end
    end
    task.wait(0.25)
    char.HumanoidRootPart.CFrame = OC
end
-- ============================================================
-- NIGHT 2
-- ============================================================
local janelasAS = {
    {posJan=Vector3.new(-292.1,82.4,-38.8), posBot=Vector3.new(-288.5,82.4,-39.6)},
    {posJan=Vector3.new(-321.9,82.4,-37.4), posBot=Vector3.new(-318.1,82.4,-40.1)},
    {posJan=Vector3.new(-311.1,82.4,38.7), posBot=Vector3.new(-313,82.6,35.4)},
    {posJan=Vector3.new(-282,82.4,-111.9), posBot=Vector3.new(-278.4,82.4,-113.3)},
    {posJan=Vector3.new(-309.6,82.4,-111.9), posBot=Vector3.new(-307.9,82.4,-113.4)},
}

local scareActive = false
local lastScare = 0
local lastMutant = nil
local DISTANCIA = 60
local COOLDOWN = 4.5

local function ativarLuz(jan)
    local char = player.Character or player.CharacterAdded:Wait()
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    char.HumanoidRootPart.CFrame = CFrame.new(jan.posBot)
    task.wait(0.3)
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("ClickDetector") then
            local parentPos = obj.Parent and obj.Parent.Position
            if parentPos and (parentPos - jan.posBot).Magnitude < 10 then
                fireclickdetector(obj)
                break
            end
        end
    end
    local fl = Instance.new("Frame")
    fl.Size = UDim2.new(1,0,1,0)
    fl.BackgroundColor3 = Color3.fromRGB(255,255,255)
    fl.BackgroundTransparency = 0.5
    fl.Parent = player.PlayerGui
    task.wait(0.2)
    fl:Destroy()
end

local function updateMutant()
    local newMutant = workspace:FindFirstChild("Mutant") or workspace:FindFirstChild("Larry")
    if newMutant and newMutant ~= lastMutant then
        lastMutant = newMutant
        lastScare = 0
    elseif not newMutant then
        lastMutant = nil
    end
end

spawn(function()
    while true do
        task.wait(1)
        if not scareActive then continue end
        updateMutant()
        if not lastMutant then continue end
        local mpos = lastMutant:FindFirstChild("HumanoidRootPart") and lastMutant.HumanoidRootPart.Position or lastMutant.Position
        local alvo, menor = nil, DISTANCIA + 1
        for _, j in pairs(janelasAS) do
            local d = (mpos - j.posJan).Magnitude
            if d < menor then menor = d; alvo = j end
        end
        if alvo and menor <= DISTANCIA and tick() - lastScare >= COOLDOWN then
            lastScare = tick()
            ativarLuz(alvo)
        end
    end
end)

local function AntiVentPests()
    local grids = workspace:FindFirstChild("Grids")
    if not grids then return end
    for _, gridel in pairs(grids:GetChildren()) do
        if gridel:IsA("Model") then
            for _, child in pairs(gridel:GetChildren()) do
                if child:IsA("Part") and child.Name == "Hitbox" then
                    child:Destroy()
                end
            end
        end
    end
end

local function ReviveN2()
    if RS and RS:FindFirstChild("Remotes") and RS.Remotes:FindFirstChild("LoadCharacter") then
        RS.Remotes.LoadCharacter:FireServer()
        task.wait(1)
    end
end

local function EscapeSnatch()
    if RS and RS:FindFirstChild("Remotes") and RS.Remotes:FindFirstChild("EscapeSnatch") then
        RS.Remotes.EscapeSnatch:FireServer()
    end
end
-- ============================================================
-- AUTO SAFE SPOT
-- ============================================================
local autoSafeActive = false
local autoSafeLoop = nil
local lastSafeTeleport = 0
local SAFE_COOLDOWN = 2
local SAFE_SPOT_CFRAME = CFrame.new(-29.2, 3, -62.8)
local MONSTROS_NAMES = {"Mutant", "Larry", "Stalker"}

local function isMonster(obj)
    if not obj:IsA("Model") then return false end
    for _, nome in pairs(MONSTROS_NAMES) do
        if obj.Name == nome then return true end
    end
    return false
end

local function getMonsterPositions()
    local positions = {}
    for _, obj in pairs(workspace:GetDescendants()) do
        if isMonster(obj) then
            local humanoid = obj:FindFirstChild("Humanoid")
            if humanoid and humanoid.Health > 0 then
                local root = obj:FindFirstChild("HumanoidRootPart") or obj:FindFirstChildWhichIsA("BasePart")
                if root then table.insert(positions, root.Position) end
            end
        end
    end
    return positions
end

local function checkAndTeleport()
    if not autoSafeActive then return end
    local char = player.Character
    if not char then return end
    local root = char:FindFirstChild("HumanoidRootPart")
    if not root then return end
    local playerPos = root.Position
    for _, pos in pairs(getMonsterPositions()) do
        local dist = (playerPos - pos).Magnitude
        if dist >= 40 and dist <= 50 and tick() - lastSafeTeleport >= SAFE_COOLDOWN then
            root.CFrame = SAFE_SPOT_CFRAME
            lastSafeTeleport = tick()
            break
        end
    end
end

local function startAutoSafe(v)
    if autoSafeLoop then autoSafeLoop:Disconnect(); autoSafeLoop = nil end
    autoSafeActive = v
    if v then
        autoSafeLoop = RunService.Stepped:Connect(checkAndTeleport)
    end
end

-- ============================================================
-- AIMBOT
-- ============================================================
local NPC_NAMES = {"Worker", "ModelWorker", "WorkerHead", "Mutant", "Larry", "Stalker", "Spider"}
local aimbotEnabled = false
local currentTarget = nil
local AIM_SPEED = 80
local MAX_DISTANCE = 80
local lastAimbotUpdate = 0

local function getNearestNPC()
    local char = player.Character
    if not char then return nil end
    local root = char:FindFirstChild("HumanoidRootPart")
    if not root then return nil end
    local charPos = root.Position
    local nearest = nil
    local shortestDist = math.huge

    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("Model") then
            local isNPC = false
            for _, nome in pairs(NPC_NAMES) do
                if obj.Name == nome then isNPC = true; break end
            end
            if not isNPC then continue end
            local humanoid = obj:FindFirstChild("Humanoid")
            if not humanoid or humanoid.Health <= 0 then continue end
            local targetPart = obj:FindFirstChild("Head") or obj:FindFirstChild("HumanoidRootPart") or obj:FindFirstChildWhichIsA("BasePart")
            if not targetPart then continue end
            local dist = (charPos - targetPart.Position).Magnitude
            if dist > MAX_DISTANCE then continue end
            if dist < shortestDist then
                shortestDist = dist
                nearest = obj
            end
        end
    end
    return nearest
end

RunService:BindToRenderStep("AimbotNPC", Enum.RenderPriority.Camera.Value + 1, function(dt)
    if not aimbotEnabled then return end
    local now = tick()
    if now - lastAimbotUpdate > 0.15 then
        lastAimbotUpdate = now
        if currentTarget and currentTarget.Parent then
            local humanoid = currentTarget:FindFirstChild("Humanoid")
            if humanoid and humanoid.Health > 0 then
                local char = player.Character
                if char then
                    local root = char:FindFirstChild("HumanoidRootPart")
                    if root then
                        local targetPart = currentTarget:FindFirstChild("Head") or currentTarget:FindFirstChild("HumanoidRootPart") or currentTarget:FindFirstChildWhichIsA("BasePart")
                        if targetPart then
                            local dist = (root.Position - targetPart.Position).Magnitude
                            if dist > MAX_DISTANCE then currentTarget = getNearestNPC() end
                        else
                            currentTarget = getNearestNPC()
                        end
                    else
                        currentTarget = getNearestNPC()
                    end
                else
                    currentTarget = getNearestNPC()
                end
            else
                currentTarget = getNearestNPC()
            end
        else
            currentTarget = getNearestNPC()
        end
    end
    if currentTarget then
        local targetPart = currentTarget:FindFirstChild("Head") or currentTarget:FindFirstChild("HumanoidRootPart") or currentTarget:FindFirstChildWhichIsA("BasePart")
        if targetPart then
            local targetPos = targetPart.Position
            local camPos = Camera.CFrame.Position
            local targetCFrame = CFrame.new(camPos, targetPos)
            local lerpSpeed = math.clamp(AIM_SPEED * dt, 0, 1)
            Camera.CFrame = Camera.CFrame:Lerp(targetCFrame, lerpSpeed)
        end
    end
end)
local function AutoMunicao()
    local char = player.Character or player.CharacterAdded:Wait()
    local root = char:WaitForChild("HumanoidRootPart")
    local ammoPiles = workspace:FindFirstChild("AmmoPiles")
    if not ammoPiles then return end
    local ammoPile = nil
    for _, A in pairs(ammoPiles:GetChildren()) do
        if A and A:FindFirstChild("Detector") and A.Detector:FindFirstChild("ClickDetector") then
            ammoPile = A; break
        end
    end
    if not ammoPile then return end
    local detector = ammoPile.Detector
    local click = detector.ClickDetector
    local originalPos = root.Position
    root.CFrame = CFrame.new(detector.Position + Vector3.new(0, 2, 0))
    task.wait(0.1)
    for i = 1, 2 do fireclickdetector(click); task.wait(0.1) end
    task.wait(0.6)
    root.CFrame = CFrame.new(originalPos)
end

-- ============================================================
-- ESP
-- ============================================================
local espPlayers = {}
local espMonster = {}

local function toggleESPPlayers(v)
    for _, h in pairs(espPlayers) do if h then h:Destroy() end end
    espPlayers = {}
    if v then
        for _, p in pairs(game.Players:GetPlayers()) do
            if p ~= player and p.Character then
                local h = Instance.new("Highlight")
                h.FillColor = Color3.fromRGB(0, 255, 0)
                h.OutlineColor = Color3.fromRGB(0, 150, 0)
                h.FillTransparency = 0.5
                h.Adornee = p.Character
                h.Parent = p.Character
                espPlayers[p] = h
            end
        end
    end
end

local function refreshMonsters()
    for _, h in pairs(espMonster) do if h then h:Destroy() end end
    espMonster = {}
    local mutant = workspace:FindFirstChild("Mutant") or workspace:FindFirstChild("Larry")
    if mutant then
        local h = Instance.new("Highlight")
        h.FillColor = Color3.fromRGB(255, 0, 0)
        h.OutlineColor = Color3.fromRGB(200, 0, 0)
        h.FillTransparency = 0.3
        h.Adornee = mutant
        h.Parent = mutant
        table.insert(espMonster, h)
    end
    local stalker = workspace:FindFirstChild("Stalker")
    if stalker then
        local h = Instance.new("Highlight")
        h.FillColor = Color3.fromRGB(255, 100, 0)
        h.OutlineColor = Color3.fromRGB(200, 80, 0)
        h.FillTransparency = 0.3
        h.Adornee = stalker
        h.Parent = stalker
        table.insert(espMonster, h)
    end
end

spawn(function()
    while true do wait(2) refreshMonsters() end
end)
workspace.ChildAdded:Connect(function(child)
    if child.Name == "Mutant" or child.Name == "Larry" or child.Name == "Stalker" then
        task.wait(0.5); refreshMonsters()
    end
end)

-- ============================================================
-- CONSTRUÇÃO DAS ABAS
-- ============================================================
-- ABA: MENU
local menuFrame = abaFramesMap["Menu"]
local menuCard = addCard(menuFrame)
addLabel(menuCard, "✦ BEM-VINDO AO DAVI HUB", Color3.fromRGB(255, 255, 255))
addLabel(menuCard, "Selecione uma aba ao lado", Color3.fromRGB(200, 200, 200))
addLabel(menuCard, "Versão 2.0", Color3.fromRGB(150, 150, 150))

-- ABA: FEEDBACK (COM SISTEMA DE FEEDBACK)
local feedbackFrame = abaFramesMap["Menu"]  -- Adiciona na aba MENU
addButton(menuCard, "📩 Enviar Feedback", function()
    abrirFeedback()
end)

-- ABA: CHARACTER (COM NOCLIP)
local charFrame = abaFramesMap["Char"]
local charCard = addCard(charFrame)
addLabel(charCard, "⚡ CHARACTER")
addToggle(charCard, "Fullbright", toggleFullbright, false)
addToggle(charCard, "Infinite Stamina", toggleStamina, false)
addToggle(charCard, "Anti-Frosted", toggleAntiTemp, false)
addToggle(charCard, "Infinite O2", toggleO2, false)
addToggle(charCard, "🔓 Noclip", toggleNoclip, false)
addSlider(charCard, "Sprint Speed", function(v)
    local ch = player.Character
    if ch then
        ch:SetAttribute("SprintSpeed", v)
        local hum = ch:FindFirstChild("Humanoid")
        if hum then hum.WalkSpeed = v end
    end
end, 17, 45, 17)

-- ABA: TELEPORTS
local teleFrame = abaFramesMap["Tele"]
local teleCard = addCard(teleFrame)
addLabel(teleCard, "📍 NIGHT 1")
for nome, cf in pairs(teleportsN1) do
    addButton(teleCard, nome, function() teleportar(cf) end)
end
addLabel(teleCard, "📍 NIGHT 2")
for nome, cf in pairs(teleportsN2) do
    addButton(teleCard, nome, function() teleportar(cf) end)
end
addLabel(teleCard, "📍 NIGHT 3")
for nome, cf in pairs(teleportsN3) do
    addButton(teleCard, nome, function() teleportar(cf) end)
end

-- ABA: NIGHT 1
local n1Frame = abaFramesMap["N1"]
local n1Card = addCard(n1Frame)
addLabel(n1Card, "🔥 NIGHT 1")
addButton(n1Card, "Refill Fireplace", RefillFireplace)
addButton(n1Card, "Grab Wood", GrabWood)
addButton(n1Card, "Refill Generator", RefillGenerator)
addButton(n1Card, "Grab JerryCan", GrabJerryCan)

-- ABA: NIGHT 2
local n2Frame = abaFramesMap["N2"]
local n2Card = addCard(n2Frame)
addLabel(n2Card, "🌙 NIGHT 2")
addToggle(n2Card, "Auto Scare", function(v) scareActive = v end, false)
addButton(n2Card, "Anti Vent-Pests", AntiVentPests)
addButton(n2Card, "Revive", ReviveN2)
addButton(n2Card, "Escape Snatch", EscapeSnatch)

-- ABA: NIGHT 3
local n3Frame = abaFramesMap["N3"]
local n3Card = addCard(n3Frame)
addLabel(n3Card, "🎯 NIGHT 3")
addToggle(n3Card, "Ativar Aimbot", function(v) aimbotEnabled = v end, false)
addSlider(n3Card, "Aimbot Speed", function(v) AIM_SPEED = v end, 30, 150, 80)
addSlider(n3Card, "Aimbot Distance", function(v) MAX_DISTANCE = v end, 30, 150, 80)
addButton(n3Card, "Auto Coletar Munição", AutoMunicao)

-- ABA: ESP
local espFrame = abaFramesMap["ESP"]
local espCard = addCard(espFrame)
addLabel(espCard, "👁️ ESP")
addToggle(espCard, "ESP Players", toggleESPPlayers, false)
addLabel(espCard, "ESP Monsters (Auto)", Color3.fromRGB(200, 200, 200))

-- ABA: AUTO
local autoFrame = abaFramesMap["Auto"]
local autoCard = addCard(autoFrame)
addLabel(autoCard, "⚡ AUTO")
addToggle(autoCard, "Auto Safe Spot", startAutoSafe, false)

-- ============================================================
-- SISTEMA DE FEEDBACK
-- ============================================================

local WEBHOOK_FEEDBACK = "https://discord.com/api/webhooks/1524546983799427194/WyTosfrV6Opc1MPOpmTJmYNlCzBu0gpRSJ89dUnqcNVYbqJ373-tCfTLUMBgOTidUEh3"

local CATEGORIAS = {
    {nome = "🐛 Bug Report", cor = 16711680},
    {nome = "💡 Sugestão", cor = 65280},
    {nome = "⭐ Elogio", cor = 16776960},
    {nome = "⚠️ Crítica", cor = 16753920},
    {nome = "❓ Dúvida", cor = 65535},
    {nome = "📱 Nova Função", cor = 16711935},
    {nome = "⚡ Performance", cor = 16777215},
    {nome = "📖 Tutorial", cor = 16777215},
    {nome = "🔧 Suporte", cor = 16777215},
    {nome = "❌ Abuso/Report", cor = 16711680},
    {nome = "📊 Estatísticas", cor = 16776960},
    {nome = "🎮 Experiência", cor = 16776960},
}

local function enviarFeedback(usuario, mensagem, categoria)
    local data = {
        ["embeds"] = {{
            ["title"] = "📩 NOVO FEEDBACK - DAVI HUB",
            ["color"] = 16753920,
            ["fields"] = {
                {["name"] = "👤 Usuário", ["value"] = usuario or "Desconhecido", ["inline"] = true},
                {["name"] = "📌 Categoria", ["value"] = categoria or "Geral", ["inline"] = true},
                {["name"] = "📝 Mensagem", ["value"] = mensagem or "Sem mensagem", ["inline"] = false},
                {["name"] = "🆔 UserID", ["value"] = tostring(player.UserId), ["inline"] = true},
                {["name"] = "🌐 Servidor", ["value"] = game.JobId or "N/A", ["inline"] = true},
                {["name"] = "📅 Data", ["value"] = os.date("%d/%m/%Y %H:%M:%S"), ["inline"] = true},
                {["name"] = "📋 Versão", ["value"] = "DAVI HUB v2.0", ["inline"] = true},
                {["name"] = "💻 Executor", ["value"] = identifyexecutor and identifyexecutor() or "Desconhecido", ["inline"] = true},
            },
            ["footer"] = {["text"] = "DAVI HUB - Sistema de Feedback v2.0"},
            ["timestamp"] = os.date("!%Y-%m-%dT%H:%M:%S.000Z")
        }}
    }
    local json = HttpService:JSONEncode(data)
    pcall(function()
        local request = syn and syn.request or request or http_request
        if request then
            request({Url = WEBHOOK_FEEDBACK, Method = "POST", Headers = {["Content-Type"] = "application/json"}, Body = json})
        end
    end)
end

local function criarGUIFeedback()
    for _, v in pairs(player.PlayerGui:GetChildren()) do if v.Name == "FeedbackGUI" then v:Destroy() end end
    local gui = Instance.new("ScreenGui")
    gui.Name = "FeedbackGUI"
    gui.Parent = player.PlayerGui
    gui.ResetOnSpawn = false
    gui.IgnoreGuiInset = true

    local fundo = Instance.new("Frame")
    fundo.Size = UDim2.new(1, 0, 1, 0)
    fundo.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    fundo.BackgroundTransparency = 0.5
    fundo.Parent = gui

    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 480, 0, 420)
    frame.Position = UDim2.new(0.5, -240, 0.5, -210)
    frame.BackgroundColor3 = Color3.fromRGB(25, 25, 40)
    frame.BackgroundTransparency = 0.05
    frame.BorderSizePixel = 0
    frame.ClipsDescendants = true
    frame.Parent = fundo

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 16)
    corner.Parent = frame

    local title = Instance.new("TextLabel")
    title.Size = UDim2.new(1, 0, 0, 50)
    title.Text = "📩 Enviar Feedback"
    title.TextColor3 = Color3.fromRGB(255, 200, 50)
    title.TextSize = 22
    title.Font = Enum.Font.GothamBold
    title.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
    title.BackgroundTransparency = 0.2
    title.Parent = frame
    local titleCorner = Instance.new("UICorner")
    titleCorner.CornerRadius = UDim.new(0, 16)
    titleCorner.Parent = title

    local close = Instance.new("TextButton")
    close.Size = UDim2.new(0, 30, 0, 30)
    close.Position = UDim2.new(1, -40, 0, 10)
    close.Text = "✕"
    close.TextColor3 = Color3.fromRGB(255, 255, 255)
    close.TextSize = 18
    close.BackgroundColor3 = Color3.fromRGB(200, 40, 40)
    close.BackgroundTransparency = 0.3
    close.BorderSizePixel = 0
    close.Parent = title
    local closeCorner = Instance.new("UICorner")
    closeCorner.CornerRadius = UDim.new(0, 8)
    closeCorner.Parent = close
    close.MouseButton1Click:Connect(function() gui:Destroy() end)

    local labelCategoria = Instance.new("TextLabel")
    labelCategoria.Size = UDim2.new(0.35, 0, 0, 25)
    labelCategoria.Position = UDim2.new(0.05, 0, 0.16, 0)
    labelCategoria.Text = "📌 Categoria:"
    labelCategoria.TextColor3 = Color3.fromRGB(200, 200, 200)
    labelCategoria.TextSize = 14
    labelCategoria.Font = Enum.Font.GothamBold
    labelCategoria.BackgroundTransparency = 1
    labelCategoria.TextXAlignment = Enum.TextXAlignment.Left
    labelCategoria.Parent = frame

    local categoriaAtual = 1
    local dropdownVisible = false

    local dropBtn = Instance.new("TextButton")
    dropBtn.Size = UDim2.new(0.50, 0, 0, 30)
    dropBtn.Position = UDim2.new(0.45, 0, 0.16, 0)
    dropBtn.Text = CATEGORIAS[1].nome
    dropBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    dropBtn.TextSize = 13
    dropBtn.Font = Enum.Font.GothamBold
    dropBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
    dropBtn.BackgroundTransparency = 0.2
    dropBtn.BorderSizePixel = 0
    dropBtn.Parent = frame
    local dropCorner = Instance.new("UICorner")
    dropCorner.CornerRadius = UDim.new(0, 8)
    dropCorner.Parent = dropBtn

    local dropMenu = Instance.new("ScrollingFrame")
    dropMenu.Size = UDim2.new(0.50, 0, 0, 0)
    dropMenu.Position = UDim2.new(0.45, 0, 0.22, 0)
    dropMenu.BackgroundColor3 = Color3.fromRGB(30, 30, 45)
    dropMenu.BackgroundTransparency = 0.1
    dropMenu.BorderSizePixel = 0
    dropMenu.ClipsDescendants = true
    dropMenu.Visible = false
    dropMenu.ScrollBarThickness = 4
    dropMenu.Parent = frame
    local dropCorner2 = Instance.new("UICorner")
    dropCorner2.CornerRadius = UDim.new(0, 8)
    dropCorner2.Parent = dropMenu

    local dropLayout = Instance.new("UIListLayout")
    dropLayout.Padding = UDim.new(0, 2)
    dropLayout.SortOrder = Enum.SortOrder.LayoutOrder
    dropLayout.Parent = dropMenu

    for i, cat in pairs(CATEGORIAS) do
        local opt = Instance.new("TextButton")
        opt.Size = UDim2.new(1, 0, 0, 30)
        opt.Text = cat.nome
        opt.TextColor3 = Color3.fromRGB(255, 255, 255)
        opt.TextSize = 13
        opt.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
        opt.BackgroundTransparency = 0.2
        opt.BorderSizePixel = 0
        opt.Parent = dropMenu
        local optCorner = Instance.new("UICorner")
        optCorner.CornerRadius = UDim.new(0, 6)
        optCorner.Parent = opt
        opt.MouseButton1Click:Connect(function()
            categoriaAtual = i
            dropBtn.Text = CATEGORIAS[i].nome
            dropdownVisible = false
            dropMenu.Visible = false
            dropMenu.Size = UDim2.new(0.50, 0, 0, 0)
        end)
    end

    dropBtn.MouseButton1Click:Connect(function()
        dropdownVisible = not dropdownVisible
        dropMenu.Visible = dropdownVisible
        if dropdownVisible then
            local totalHeight = #CATEGORIAS * 32
            local maxHeight = 160
            dropMenu.Size = UDim2.new(0.50, 0, 0, math.min(totalHeight, maxHeight))
            dropMenu.CanvasSize = UDim2.new(0, 0, 0, totalHeight)
        else
            dropMenu.Size = UDim2.new(0.50, 0, 0, 0)
        end
    end)

    local labelMsg = Instance.new("TextLabel")
    labelMsg.Size = UDim2.new(0.9, 0, 0, 25)
    labelMsg.Position = UDim2.new(0.05, 0, 0.30, 0)
    labelMsg.Text = "📝 Mensagem:"
    labelMsg.TextColor3 = Color3.fromRGB(200, 200, 200)
    labelMsg.TextSize = 14
    labelMsg.Font = Enum.Font.GothamBold
    labelMsg.BackgroundTransparency = 1
    labelMsg.TextXAlignment = Enum.TextXAlignment.Left
    labelMsg.Parent = frame

    local msgBox = Instance.new("TextBox")
    msgBox.Size = UDim2.new(0.9, 0, 0, 110)
    msgBox.Position = UDim2.new(0.05, 0, 0.37, 0)
    msgBox.BackgroundColor3 = Color3.fromRGB(35, 35, 50)
    msgBox.BackgroundTransparency = 0.2
    msgBox.TextColor3 = Color3.fromRGB(255, 255, 255)
    msgBox.TextSize = 14
    msgBox.Font = Enum.Font.Gotham
    msgBox.Text = "Escreva sua mensagem aqui..."
    msgBox.TextWrapped = true
    msgBox.TextXAlignment = Enum.TextXAlignment.Left
    msgBox.TextYAlignment = Enum.TextYAlignment.Top
    msgBox.ClearTextOnFocus = true
    msgBox.BorderSizePixel = 0
    msgBox.Parent = frame
    local msgCorner = Instance.new("UICorner")
    msgCorner.CornerRadius = UDim.new(0, 8)
    msgCorner.Parent = msgBox

    local btnEnviar = Instance.new("TextButton")
    btnEnviar.Size = UDim2.new(0.4, 0, 0, 40)
    btnEnviar.Position = UDim2.new(0.05, 0, 0.75, 0)
    btnEnviar.Text = "📤 Enviar"
    btnEnviar.TextColor3 = Color3.fromRGB(255, 255, 255)
    btnEnviar.TextSize = 16
    btnEnviar.Font = Enum.Font.GothamBold
    btnEnviar.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
    btnEnviar.BackgroundTransparency = 0.2
    btnEnviar.BorderSizePixel = 0
    btnEnviar.Parent = frame
    local btnCorner = Instance.new("UICorner")
    btnCorner.CornerRadius = UDim.new(0, 8)
    btnCorner.Parent = btnEnviar

    local btnCancelar = Instance.new("TextButton")
    btnCancelar.Size = UDim2.new(0.4, 0, 0, 40)
    btnCancelar.Position = UDim2.new(0.55, 0, 0.75, 0)
    btnCancelar.Text = "❌ Cancelar"
    btnCancelar.TextColor3 = Color3.fromRGB(255, 255, 255)
    btnCancelar.TextSize = 16
    btnCancelar.Font = Enum.Font.GothamBold
    btnCancelar.BackgroundColor3 = Color3.fromRGB(200, 40, 40)
    btnCancelar.BackgroundTransparency = 0.2
    btnCancelar.BorderSizePixel = 0
    btnCancelar.Parent = frame
    local btnCancelarCorner = Instance.new("UICorner")
    btnCancelarCorner.CornerRadius = UDim.new(0, 8)
    btnCancelarCorner.Parent = btnCancelar
    btnCancelar.MouseButton1Click:Connect(function() gui:Destroy() end)

    local statusLabel = Instance.new("TextLabel")
    statusLabel.Size = UDim2.new(0.9, 0, 0, 25)
    statusLabel.Position = UDim2.new(0.05, 0, 0.88, 0)
    statusLabel.Text = "✅ Selecione uma categoria e escreva sua mensagem!"
    statusLabel.TextColor3 = Color3.fromRGB(100, 255, 100)
    statusLabel.TextSize = 13
    statusLabel.Font = Enum.Font.Gotham
    statusLabel.BackgroundTransparency = 1
    statusLabel.Parent = frame

    btnEnviar.MouseButton1Click:Connect(function()
        local mensagem = msgBox.Text
        if mensagem == "" or mensagem == "Escreva sua mensagem aqui..." then
            statusLabel.Text = "❌ Digite uma mensagem!"
            statusLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
            return
        end
        local categoria = CATEGORIAS[categoriaAtual].nome
        statusLabel.Text = "⏳ Enviando..."
        statusLabel.TextColor3 = Color3.fromRGB(255, 255, 100)
        enviarFeedback(player.Name, mensagem, categoria)
        statusLabel.Text = "✅ Feedback enviado! Obrigado! 🙏"
        statusLabel.TextColor3 = Color3.fromRGB(100, 255, 100)
        msgBox.Text = ""
        btnEnviar.Text = "✅ Enviado!"
        btnEnviar.BackgroundColor3 = Color3.fromRGB(0, 200, 0)
        task.wait(2)
        gui:Destroy()
    end)

    msgBox.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            btnEnviar.MouseButton1Click:Fire()
        end
    end)
end

local function abrirFeedback()
    criarGUIFeedback()
end
-- ============================================================
-- SISTEMA DE IDENTIFICAÇÃO DE USUÁRIOS DO DAVI HUB
-- ============================================================

-- ============================================================
-- FUNÇÃO PARA IDENTIFICAR USUÁRIOS DO DAVI HUB
-- ============================================================
local function identificarUsuarios()
    local usuarios = {}
    local jogadores = game.Players:GetPlayers()
    
    for _, jogador in pairs(jogadores) do
        if jogador ~= player then
            -- Verifica se o jogador tem a GUI do DAVI HUB
            local sucesso, resultado = pcall(function()
                local playerGui = jogador:FindFirstChild("PlayerGui")
                if playerGui then
                    -- Procura por qualquer GUI com nome relacionado ao DAVI HUB
                    for _, gui in pairs(playerGui:GetChildren()) do
                        if gui:IsA("ScreenGui") then
                            local nome = gui.Name:lower()
                            if nome:find("davi") or nome:find("hub") or nome:find("davihub") then
                                table.insert(usuarios, {
                                    nome = jogador.Name,
                                    userId = jogador.UserId,
                                    gui = gui.Name
                                })
                                break
                            end
                        end
                    end
                end
            end)
        end
    end
    
    return usuarios
end

-- ============================================================
-- FUNÇÃO PARA MOSTRAR USUÁRIOS NO CONSOLE
-- ============================================================
local function mostrarUsuariosConsole()
    local usuarios = identificarUsuarios()
    print("")
    print("===== 👥 USUÁRIOS DO DAVI HUB NO SERVIDOR =====")
    
    if #usuarios == 0 then
        print("📌 Nenhum outro usuário do DAVI HUB encontrado.")
        print("💡 Você é o único usando o HUB neste servidor!")
    else
        print("📊 Total de usuários: " .. #usuarios)
        print("")
        for i, usuario in pairs(usuarios) do
            print("🔹 " .. i .. ". " .. usuario.nome .. " (ID: " .. usuario.userId .. ")")
            print("   📌 GUI: " .. usuario.gui)
        end
    end
    print("================================================")
    print("")
end

-- ============================================================
-- FUNÇÃO PARA CRIAR UMA GUI COM A LISTA DE USUÁRIOS
-- ============================================================
local function criarGUIUsuarios()
    -- Fecha a GUI antiga se existir
    for _, v in pairs(player.PlayerGui:GetChildren()) do
        if v.Name == "UsuariosDAVI" then v:Destroy() end
    end
    
    local usuarios = identificarUsuarios()
    
    local gui = Instance.new("ScreenGui")
    gui.Name = "UsuariosDAVI"
    gui.Parent = player.PlayerGui
    gui.ResetOnSpawn = false
    gui.IgnoreGuiInset = true
    
    -- Fundo semi-transparente
    local fundo = Instance.new("Frame")
    fundo.Size = UDim2.new(1, 0, 1, 0)
    fundo.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    fundo.BackgroundTransparency = 0.5
    fundo.Parent = gui
    
    -- Janela principal
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 350, 0, 300)
    frame.Position = UDim2.new(0.5, -175, 0.5, -150)
    frame.BackgroundColor3 = Color3.fromRGB(20, 20, 35)
    frame.BackgroundTransparency = 0.05
    frame.BorderSizePixel = 0
    frame.ClipsDescendants = true
    frame.Parent = fundo
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 16)
    corner.Parent = frame
    
    -- Contorno
    local border = Instance.new("UIStroke")
    border.Color = Color3.fromRGB(255, 140, 0)
    border.Thickness = 2
    border.Transparency = 0.3
    border.Parent = frame
    
    -- Título
    local title = Instance.new("TextLabel")
    title.Size = UDim2.new(1, 0, 0, 45)
    title.Text = "👥 USUÁRIOS DO DAVI HUB"
    title.TextColor3 = Color3.fromRGB(255, 200, 50)
    title.TextSize = 18
    title.Font = Enum.Font.GothamBold
    title.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
    title.BackgroundTransparency = 0.15
    title.Parent = frame
    
    local titleCorner = Instance.new("UICorner")
    titleCorner.CornerRadius = UDim.new(0, 16)
    titleCorner.Parent = title
    
    -- Botão Fechar
    local close = Instance.new("TextButton")
    close.Size = UDim2.new(0, 30, 0, 30)
    close.Position = UDim2.new(1, -38, 0, 7)
    close.Text = "✕"
    close.TextColor3 = Color3.fromRGB(255, 255, 255)
    close.TextSize = 18
    close.BackgroundColor3 = Color3.fromRGB(200, 40, 40)
    close.BackgroundTransparency = 0.3
    close.BorderSizePixel = 0
    close.Parent = title
    
    local closeCorner = Instance.new("UICorner")
    closeCorner.CornerRadius = UDim.new(0, 8)
    closeCorner.Parent = close
    close.MouseButton1Click:Connect(function() gui:Destroy() end)
    
    -- Lista de usuários (ScrollingFrame)
    local lista = Instance.new("ScrollingFrame")
    lista.Size = UDim2.new(1, -20, 1, -80)
    lista.Position = UDim2.new(0, 10, 0, 55)
    lista.BackgroundTransparency = 1
    lista.BorderSizePixel = 0
    lista.ScrollBarThickness = 6
    lista.ScrollBarImageColor3 = Color3.fromRGB(255, 140, 0)
    lista.Parent = frame
    
    local listaLayout = Instance.new("UIListLayout")
    listaLayout.Padding = UDim.new(0, 4)
    listaLayout.SortOrder = Enum.SortOrder.LayoutOrder
    listaLayout.Parent = lista
    
    -- Contador
    local contador = Instance.new("TextLabel")
    contador.Size = UDim2.new(1, 0, 0, 25)
    contador.Position = UDim2.new(0, 0, 1, -35)
    contador.Text = "📊 Total: " .. #usuarios .. " usuário(s)"
    contador.TextColor3 = Color3.fromRGB(200, 200, 200)
    contador.TextSize = 14
    contador.Font = Enum.Font.Gotham
    contador.BackgroundTransparency = 1
    contador.Parent = frame
    
    -- Adiciona os usuários na lista
    if #usuarios == 0 then
        local nenhum = Instance.new("TextLabel")
        nenhum.Size = UDim2.new(1, 0, 0, 35)
        nenhum.Text = "🔹 Nenhum outro usuário do DAVI HUB encontrado."
        nenhum.TextColor3 = Color3.fromRGB(200, 200, 200)
        nenhum.TextSize = 14
        nenhum.Font = Enum.Font.Gotham
        nenhum.BackgroundTransparency = 1
        nenhum.Parent = lista
    else
        for _, usuario in pairs(usuarios) do
            local item = Instance.new("TextLabel")
            item.Size = UDim2.new(1, 0, 0, 30)
            item.Text = "🔹 " .. usuario.nome
            item.TextColor3 = Color3.fromRGB(255, 255, 255)
            item.TextSize = 14
            item.Font = Enum.Font.GothamBold
            item.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
            item.BackgroundTransparency = 0.2
            item.BorderSizePixel = 0
            item.Parent = lista
            
            local itemCorner = Instance.new("UICorner")
            itemCorner.CornerRadius = UDim.new(0, 6)
            itemCorner.Parent = item
        end
    end
    
    -- Atualiza o CanvasSize da lista
    task.wait(0.1)
    local totalHeight = #usuarios * 34 + 10
    if #usuarios == 0 then totalHeight = 50 end
    lista.CanvasSize = UDim2.new(0, 0, 0, totalHeight)
end

-- ============================================================
-- COMANDO NO CHAT PARA VER USUÁRIOS (/davi ou /hub)
-- ============================================================
game:GetService("Players").LocalPlayer.Chatted:Connect(function(msg)
    if msg:lower() == "/davi" or msg:lower() == "/hub" then
        criarGUIUsuarios()
        mostrarUsuariosConsole()
    end
end)

-- ============================================================
-- FUNÇÃO PARA ATUALIZAR AUTOMATICAMENTE A CADA 30 SEGUNDOS
-- ============================================================
spawn(function()
    while true do
        task.wait(30)
        -- Mostra no console a cada 30 segundos (opcional)
        -- mostrarUsuariosConsole()
    end
end)

-- ============================================================
-- ADICIONAR BOTÃO NA ABA MENU
-- ============================================================
-- Adicione na aba MENU (depois do menuCard)
addButton(menuCard, "👥 Ver Usuários do HUB", function()
    criarGUIUsuarios()
    mostrarUsuariosConsole()
end)

print("✅ Sistema de identificação de usuários carregado!")
print("💡 Digite /davi ou /hub no chat para ver quem está usando o DAVI HUB")
-- ============================================================
-- TAG LARANJA NO CHAT PARA USUÁRIOS DO DAVI HUB
-- ============================================================

-- Verifica se o jogador que falou está usando o DAVI HUB
local function isDaviUser(jogador)
    local sucesso, resultado = pcall(function()
        local playerGui = jogador:FindFirstChild("PlayerGui")
        if playerGui then
            for _, gui in pairs(playerGui:GetChildren()) do
                if gui:IsA("ScreenGui") then
                    local nome = gui.Name:lower()
                    if nome:find("davi") or nome:find("hub") or nome:find("davihub") then
                        return true
                    end
                end
            end
        end
    end)
    return false
end

-- Cria um TextLabel na tela para exibir o chat personalizado
local function criarChatCustomizado()
    -- Remove chat antigo se existir
    for _, v in pairs(player.PlayerGui:GetChildren()) do
        if v.Name == "DaviChat" then v:Destroy() end
    end
    
    local chatGui = Instance.new("ScreenGui")
    chatGui.Name = "DaviChat"
    chatGui.Parent = player.PlayerGui
    chatGui.ResetOnSpawn = false
    chatGui.IgnoreGuiInset = true
    
    -- Frame que vai conter as mensagens (ScrollingFrame)
    local chatFrame = Instance.new("ScrollingFrame")
    chatFrame.Size = UDim2.new(0, 400, 0, 200)
    chatFrame.Position = UDim2.new(0.02, 0, 0.5, -100) -- Canto inferior esquerdo
    chatFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    chatFrame.BackgroundTransparency = 0.5
    chatFrame.BorderSizePixel = 0
    chatFrame.ClipsDescendants = true
    chatFrame.ScrollBarThickness = 4
    chatFrame.ScrollBarImageColor3 = Color3.fromRGB(255, 140, 0)
    chatFrame.Parent = chatGui
    
    -- Layout para organizar as mensagens
    local chatLayout = Instance.new("UIListLayout")
    chatLayout.Padding = UDim.new(0, 2)
    chatLayout.SortOrder = Enum.SortOrder.LayoutOrder
    chatLayout.Parent = chatFrame
    
    -- Mantém as últimas 20 mensagens
    local mensagens = {}
    
    -- Função para adicionar mensagem ao chat
    local function adicionarMensagem(jogador, mensagem, isDavi)
        local label = Instance.new("TextLabel")
        label.Size = UDim2.new(1, 0, 0, 20)
        label.Text = ""
        label.TextColor3 = Color3.fromRGB(255, 255, 255)
        label.TextSize = 14
        label.Font = Enum.Font.Gotham
        label.BackgroundTransparency = 1
        label.TextXAlignment = Enum.TextXAlignment.Left
        label.Parent = chatFrame
        
        -- Define o texto com ou sem tag
        if isDavi then
            label.Text = "🔥 " .. jogador.Name .. ": " .. mensagem
            -- Só a tag é laranja, o resto é branco
            -- Vamos usar TextLabel com RichText
            label.RichText = true
            label.Text = '<font color="#FF8C00">[DAVI]</font> <font color="#FFFFFF">' .. jogador.Name .. ': ' .. mensagem .. '</font>'
        else
            label.Text = jogador.Name .. ": " .. mensagem
        end
        
        -- Limita a quantidade de mensagens
        table.insert(mensagens, label)
        if #mensagens > 20 then
            local remover = table.remove(mensagens, 1)
            remover:Destroy()
        end
        
        -- Atualiza o scroll para a última mensagem
        task.wait(0.05)
        chatFrame.CanvasSize = UDim2.new(0, 0, 0, #mensagens * 22 + 10)
        chatFrame.ScrollPosition = Vector2.new(0, chatFrame.CanvasSize.Y.Offset)
    end
    
    -- Escuta as mensagens de todos os jogadores
    game:GetService("Players").PlayerAdded:Connect(function(novoJogador)
        novoJogador.Chatted:Connect(function(mensagem)
            local isDavi = isDaviUser(novoJogador)
            adicionarMensagem(novoJogador, mensagem, isDavi)
        end)
    end)
    
    -- Para jogadores já existentes
    for _, jogador in pairs(game.Players:GetPlayers()) do
        if jogador ~= player then
            jogador.Chatted:Connect(function(mensagem)
                local isDavi = isDaviUser(jogador)
                adicionarMensagem(jogador, mensagem, isDavi)
            end)
        end
    end
    
    -- O próprio jogador também
    player.Chatted:Connect(function(mensagem)
        adicionarMensagem(player, mensagem, true) -- Sempre true para o próprio
    end)
    
    return chatGui
end

-- Inicia o chat customizado
criarChatCustomizado()

print("✅ Chat com tag laranja para usuários do DAVI HUB ativado!")
-- ============================================================
-- FORÇA ATUALIZAÇÃO DO SCROLL
-- ============================================================
task.wait(0.5)
for _, frame in pairs(abaFrames) do
    local flayout = frame:FindFirstChildWhichIsA("UIListLayout")
    if flayout then
        frame.CanvasSize = UDim2.new(0, 0, 0, flayout.AbsoluteContentSize.Y + 20)
    end
end
