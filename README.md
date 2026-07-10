
local player = game.Players.LocalPlayer
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local Camera = workspace.CurrentCamera
local UserInputService = game:GetService("UserInputService")
local RS = game.ReplicatedStorage
local HttpService = game:GetService("HttpService")
local TweenService = game:GetService("TweenService")

-- ============================================================
-- SISTEMA DE KEY (VALIDAÇÃO VIA PASTEBIN)
-- ============================================================

local WEBHOOK_URL = "https://discord.com/api/webhooks/1524546983799427194/WyTosfrV6Opc1MPOpmTJmYNlCzBu0gpRSJ89dUnqcNVYbqJ373-tCfTLUMBgOTidUEh3"
local PASTEBIN_URL = "https://pastebin.com/raw/rFhbk945"
local VIP_DEV_KEY = "Exp,ri?me?ntd?ave123191717172827181"

local keyValidada = false

-- Tabela de expiração das keys
local EXPIRACAO_KEYS = {
    ["free_10182alapapqaoqkfa"] = "31/12/2025",
    ["free_20394blbqbqbrbrlsb"] = "31/12/2025",
    ["free_30567cmcrcrcscsmtc"] = "31/12/2025",
    ["free_40821dndsdtdudunud"] = "31/12/2025",
    ["free_50943eoeuevevevove"] = "31/12/2025",
    ["free_61054fpfwfwfwfwpwf"] = "31/12/2025",
    ["free_71265gqgxgxgxgxqgx"] = "31/12/2025",
    ["free_81376hrhyhyhyhyrhy"] = "31/12/2025",
    ["free_91487isizizizizsiz"] = "31/12/2025",
    ["free_101598jtjajajajataj"] = "31/12/2025",
}

local function isKeyExpirada(key)
    local dataExp = EXPIRACAO_KEYS[key]
    if not dataExp then return true end
    local dia, mes, ano = dataExp:match("(%d+)/(%d+)/(%d+)")
    if not dia then return true end
    local expiraTimestamp = os.time({
        day = tonumber(dia),
        month = tonumber(mes),
        year = tonumber(ano),
        hour = 23, min = 59, sec = 59
    })
    return os.time() > expiraTimestamp
end

local function baixarListaKeys()
    local sucesso, conteudo = pcall(function()
        return game:HttpGet(PASTEBIN_URL)
    end)
    if sucesso and conteudo then
        local keys = {}
        for key in string.gmatch(conteudo, "%S+") do
            if key ~= "" then keys[key] = true end
        end
        return keys
    end
    return nil
end

local function autoSaveKey(key)
    if key and key ~= "" then
        player:SetAttribute("DAVI_KEY_ATIVADA", key)
        player:SetAttribute("DAVI_KEY_DATA", os.date("%d/%m/%Y %H:%M:%S"))
        print("💾 Key salva: " .. key)
    end
end

local function verificarKeySalva()
    local keySalva = player:GetAttribute("DAVI_KEY_ATIVADA")
    if not keySalva then return false end
    if isKeyExpirada(keySalva) then
        print("⏰ KEY EXPIRADA!")
        player:SetAttribute("DAVI_KEY_ATIVADA", nil)
        return false
    end
    local keysOnline = baixarListaKeys()
    if keysOnline and not keysOnline[keySalva] then
        print("❌ KEY REVOGADA!")
        player:SetAttribute("DAVI_KEY_ATIVADA", nil)
        return false
    end
    return true
end

local function verificarKey(key)
    if key == VIP_DEV_KEY then
        return true, "KEY VIP DE DESENVOLVEDOR!"
    end
    if isKeyExpirada(key) then
        return false, "⏰ KEY EXPIRADA!"
    end
    local keysOnline = baixarListaKeys()
    if keysOnline then
        if keysOnline[key] then
            autoSaveKey(key)
            return true, "KEY VÁLIDA!"
        else
            return false, "KEY INVÁLIDA OU REVOGADA!"
        end
    else
        return false, "ERRO AO VALIDAR KEY!"
    end
end

local function statusKey()
    local key = player:GetAttribute("DAVI_KEY_ATIVADA")
    if not key then
        print("❌ Nenhuma key ativada.")
        return
    end
    local data = player:GetAttribute("DAVI_KEY_DATA")
    local expirada = isKeyExpirada(key)
    print("=== STATUS DA KEY ===")
    print("🔑 Key: " .. key)
    print("📅 Ativada em: " .. (data or "N/A"))
    print(expirada and "⏰ Status: EXPIRADA!" or "✅ Status: VÁLIDA")
    print("======================")
end
-- ============================================================
-- SISTEMA DE IDIOMAS
-- ============================================================

local IDIOMAS = {
    pt = {
        nome = "Português",
        main = "PRINCIPAL", menu = "MENU", tele = "TELEPORTES",
        n1 = "NOITE 1", n2 = "NOITE 2", n3 = "NOITE 3",
        esp = "ESP", geral = "GERAL", config = "CONFIGURAÇÕES",
        fullbright = "Fullbright", stamina = "Stamina Infinita",
        antifrost = "Anti-Frosted", o2 = "O2 Infinito",
        noclip = "Noclip", sprint = "Velocidade da Sprint",
        feedback = "Enviar Feedback", usuarios = "Ver Usuários do HUB",
        autoscare = "Auto Scare", antivent = "Anti Vent",
        revive = "Reviver", escapesnatch = "Escape Snatch",
        refillpower = "Recarregar Energia", antistalker = "Anti Stalker",
        aimbot = "Ativar Aimbot", aimspeed = "Velocidade do Aimbot",
        aimdist = "Distância do Aimbot", municao = "Auto Coletar Munição",
        esp_players = "ESP Players", esp_larry = "ESP Larry",
        esp_stalker = "ESP Stalker", esp_zombie = "ESP Zumbis & Esqueletos",
        bypass = "Bypass Anti-Cheat", static = "Desabilitar Estática",
        notifier = "Notificador", salvar = "Salvar Configurações",
        carregar = "Carregar Configurações", autoload = "Auto Load",
        idioma = "Idioma", config_salvas = "As configurações são salvas em:",
        arquivo_config = "DAVI_HUB_Config.json",
        bemvindo = "Bem-vindo ao DAVI HUB",
        selecione = "Selecione uma aba ao lado",
        versao = "Versão 2.0",
        idioma_alterado = "Idioma alterado para: ",
    },
    en = {
        nome = "English",
        main = "MAIN", menu = "MENU", tele = "TELEPORTS",
        n1 = "NIGHT 1", n2 = "NIGHT 2", n3 = "NIGHT 3",
        esp = "ESP", geral = "GENERAL", config = "SETTINGS",
        fullbright = "Fullbright", stamina = "Infinite Stamina",
        antifrost = "Anti-Frosted", o2 = "Infinite O2",
        noclip = "Noclip", sprint = "Sprint Speed",
        feedback = "Send Feedback", usuarios = "View HUB Users",
        autoscare = "Auto Scare", antivent = "Anti Vent",
        revive = "Revive", escapesnatch = "Escape Snatch",
        refillpower = "Refill Power", antistalker = "Anti Stalker",
        aimbot = "Enable Aimbot", aimspeed = "Aimbot Speed",
        aimdist = "Aimbot Distance", municao = "Auto Collect Ammo",
        esp_players = "ESP Players", esp_larry = "ESP Larry",
        esp_stalker = "ESP Stalker", esp_zombie = "ESP Zombies & Skeletons",
        bypass = "Bypass Anti-Cheat", static = "Disable Static",
        notifier = "Notifier", salvar = "Save Settings",
        carregar = "Load Settings", autoload = "Auto Load",
        idioma = "Language", config_salvas = "Settings are saved at:",
        arquivo_config = "DAVI_HUB_Config.json",
        bemvindo = "Welcome to DAVI HUB",
        selecione = "Select a tab on the side",
        versao = "Version 2.0",
        idioma_alterado = "Language changed to: ",
    },
    ["pt-pt"] = {
        nome = "Português (PT)",
        main = "PRINCIPAL", menu = "MENU", tele = "TELEPORTES",
        n1 = "NOITE 1", n2 = "NOITE 2", n3 = "NOITE 3",
        esp = "ESP", geral = "GERAL", config = "CONFIGURAÇÕES",
        fullbright = "Fullbright", stamina = "Stamina Infinita",
        antifrost = "Anti-Frosted", o2 = "O2 Infinito",
        noclip = "Noclip", sprint = "Velocidade da Sprint",
        feedback = "Enviar Feedback", usuarios = "Ver Utilizadores do HUB",
        autoscare = "Auto Scare", antivent = "Anti Vent",
        revive = "Reviver", escapesnatch = "Escape Snatch",
        refillpower = "Recarregar Energia", antistalker = "Anti Stalker",
        aimbot = "Ativar Aimbot", aimspeed = "Velocidade do Aimbot",
        aimdist = "Distância do Aimbot", municao = "Auto Colecionar Munição",
        esp_players = "ESP Jogadores", esp_larry = "ESP Larry",
        esp_stalker = "ESP Stalker", esp_zombie = "ESP Zumbis & Esqueletos",
        bypass = "Bypass Anti-Cheat", static = "Desabilitar Estática",
        notifier = "Notificador", salvar = "Guardar Configurações",
        carregar = "Carregar Configurações", autoload = "Auto Load",
        idioma = "Idioma", config_salvas = "As configurações são guardadas em:",
        arquivo_config = "DAVI_HUB_Config.json",
        bemvindo = "Bem-vindo ao DAVI HUB",
        selecione = "Selecione uma aba ao lado",
        versao = "Versão 2.0",
        idioma_alterado = "Idioma alterado para: ",
    },
    es = {
        nome = "Español",
        main = "PRINCIPAL", menu = "MENÚ", tele = "TELETRANSPORTES",
        n1 = "NOCHE 1", n2 = "NOCHE 2", n3 = "NOCHE 3",
        esp = "ESP", geral = "GENERAL", config = "CONFIGURACIÓN",
        fullbright = "Fullbright", stamina = "Resistencia Infinita",
        antifrost = "Anti-Frosted", o2 = "O2 Infinito",
        noclip = "Noclip", sprint = "Velocidad de Sprint",
        feedback = "Enviar Feedback", usuarios = "Ver Usuarios del HUB",
        autoscare = "Auto Scare", antivent = "Anti Vent",
        revive = "Revivir", escapesnatch = "Escape Snatch",
        refillpower = "Recargar Energía", antistalker = "Anti Stalker",
        aimbot = "Activar Aimbot", aimspeed = "Velocidad del Aimbot",
        aimdist = "Distancia del Aimbot", municao = "Auto Recoger Munición",
        esp_players = "ESP Jugadores", esp_larry = "ESP Larry",
        esp_stalker = "ESP Stalker", esp_zombie = "ESP Zombies & Esqueletos",
        bypass = "Bypass Anti-Cheat", static = "Desactivar Estática",
        notifier = "Notificador", salvar = "Guardar Configuración",
        carregar = "Cargar Configuración", autoload = "Auto Load",
        idioma = "Idioma", config_salvas = "La configuración se guarda en:",
        arquivo_config = "DAVI_HUB_Config.json",
        bemvindo = "Bienvenido a DAVI HUB",
        selecione = "Seleccione una pestaña al lado",
        versao = "Versión 2.0",
        idioma_alterado = "Idioma cambiado a: ",
    },
    ru = {
        nome = "Русский",
        main = "ГЛАВНАЯ", menu = "МЕНЮ", tele = "ТЕЛЕПОРТЫ",
        n1 = "НОЧЬ 1", n2 = "НОЧЬ 2", n3 = "НОЧЬ 3",
        esp = "ESP", geral = "ОБЩЕЕ", config = "НАСТРОЙКИ",
        fullbright = "Fullbright", stamina = "Бесконечная Выносливость",
        antifrost = "Anti-Frosted", o2 = "Бесконечный O2",
        noclip = "Noclip", sprint = "Скорость Спринта",
        feedback = "Отправить Отзыв", usuarios = "Посмотреть Пользователей HUB",
        autoscare = "Auto Scare", antivent = "Anti Vent",
        revive = "Воскресить", escapesnatch = "Escape Snatch",
        refillpower = "Перезарядка Энергии", antistalker = "Anti Stalker",
        aimbot = "Включить Aimbot", aimspeed = "Скорость Aimbot",
        aimdist = "Дистанция Aimbot", municao = "Автосбор Патронов",
        esp_players = "ESP Игроков", esp_larry = "ESP Larry",
        esp_stalker = "ESP Stalker", esp_zombie = "ESP Зомби & Скелеты",
        bypass = "Bypass Anti-Cheat", static = "Отключить Статику",
        notifier = "Уведомитель", salvar = "Сохранить Настройки",
        carregar = "Загрузить Настройки", autoload = "Автозагрузка",
        idioma = "Язык", config_salvas = "Настройки сохраняются в:",
        arquivo_config = "DAVI_HUB_Config.json",
        bemvindo = "Добро пожаловать в DAVI HUB",
        selecione = "Выберите вкладку сбоку",
        versao = "Версия 2.0",
        idioma_alterado = "Язык изменен на: ",
    },
}

local idiomaAtual = "pt"
local listaIdiomas = {
    {nome = "Português (BR)", codigo = "pt"},
    {nome = "English", codigo = "en"},
    {nome = "Português (PT)", codigo = "pt-pt"},
    {nome = "Español", codigo = "es"},
    {nome = "Русский", codigo = "ru"},
}

local function t(chave)
    if IDIOMAS[idiomaAtual] and IDIOMAS[idiomaAtual][chave] then
        return IDIOMAS[idiomaAtual][chave]
    end
    return chave
end
-- ============================================================
-- SISTEMA DE CONFIGURAÇÕES
-- ============================================================

local configs = {
    idioma = "pt",
    noclip = false,
    fullbright = false,
    stamina = false,
    antifrost = false,
    o2 = false,
    aimbot = false,
    autoscare = false,
    esp_players = false,
    esp_larry = false,
    esp_stalker = false,
    esp_zombie = false,
    notifier = false,
    sprint_speed = 17,
    aim_speed = 80,
    aim_distance = 80,
    autoload = true,
}

local function salvarConfiguracoes()
    local dados = {
        idioma = idiomaAtual,
        noclip = configs.noclip,
        fullbright = configs.fullbright,
        stamina = configs.stamina,
        antifrost = configs.antifrost,
        o2 = configs.o2,
        aimbot = configs.aimbot,
        autoscare = configs.autoscare,
        esp_players = configs.esp_players,
        esp_larry = configs.esp_larry,
        esp_stalker = configs.esp_stalker,
        esp_zombie = configs.esp_zombie,
        notifier = configs.notifier,
        sprint_speed = configs.sprint_speed,
        aim_speed = configs.aim_speed,
        aim_distance = configs.aim_distance,
        autoload = configs.autoload,
    }
    local json = HttpService:JSONEncode(dados)
    pcall(function()
        writefile("DAVI_HUB_Config.json", json)
        print("💾 Configurações salvas!")
    end)
end

local function carregarConfiguracoes()
    local sucesso, dados = pcall(function()
        return readfile("DAVI_HUB_Config.json")
    end)
    if sucesso and dados then
        local config = HttpService:JSONDecode(dados)
        idiomaAtual = config.idioma or "pt"
        configs.noclip = config.noclip or false
        configs.fullbright = config.fullbright or false
        configs.stamina = config.stamina or false
        configs.antifrost = config.antifrost or false
        configs.o2 = config.o2 or false
        configs.aimbot = config.aimbot or false
        configs.autoscare = config.autoscare or false
        configs.esp_players = config.esp_players or false
        configs.esp_larry = config.esp_larry or false
        configs.esp_stalker = config.esp_stalker or false
        configs.esp_zombie = config.esp_zombie or false
        configs.notifier = config.notifier or false
        configs.sprint_speed = config.sprint_speed or 17
        configs.aim_speed = config.aim_speed or 80
        configs.aim_distance = config.aim_distance or 80
        configs.autoload = (config.autoload == nil and true) or config.autoload
        print("📂 Configurações carregadas!")
        return true
    else
        return false
    end
end

if configs.autoload then
    carregarConfiguracoes()
end
-- ============================================================
-- SISTEMA DE IDENTIFICAÇÃO DE USUÁRIOS
-- ============================================================

local function identificarUsuarios()
    local usuarios = {}
    for _, jogador in pairs(game.Players:GetPlayers()) do
        if jogador ~= player then
            pcall(function()
                local playerGui = jogador:FindFirstChild("PlayerGui")
                if playerGui then
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

local function mostrarUsuariosConsole()
    local usuarios = identificarUsuarios()
    print("")
    print("===== USUARIOS DO DAVI HUB NO SERVIDOR =====")
    if #usuarios == 0 then
        print("Nenhum outro usuario do DAVI HUB encontrado.")
    else
        print("Total de usuarios: " .. #usuarios)
        for i, usuario in pairs(usuarios) do
            print(i .. ". " .. usuario.nome .. " (ID: " .. usuario.userId .. ")")
        end
    end
    print("================================================")
end

local function criarGUIUsuarios()
    for _, v in pairs(player.PlayerGui:GetChildren()) do
        if v.Name == "UsuariosDAVI" then v:Destroy() end
    end
    
    local usuarios = identificarUsuarios()
    local gui = Instance.new("ScreenGui")
    gui.Name = "UsuariosDAVI"
    gui.Parent = player.PlayerGui
    gui.ResetOnSpawn = false
    gui.IgnoreGuiInset = true
    
    local fundo = Instance.new("Frame")
    fundo.Size = UDim2.new(1, 0, 1, 0)
    fundo.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    fundo.BackgroundTransparency = 0.5
    fundo.Parent = gui
    
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
    
    local border = Instance.new("UIStroke")
    border.Color = Color3.fromRGB(255, 140, 0)
    border.Thickness = 2
    border.Transparency = 0.3
    border.Parent = frame
    
    local title = Instance.new("TextLabel")
    title.Size = UDim2.new(1, 0, 0, 45)
    title.Text = "USUARIOS DO DAVI HUB"
    title.TextColor3 = Color3.fromRGB(255, 200, 50)
    title.TextSize = 18
    title.Font = Enum.Font.GothamBold
    title.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
    title.BackgroundTransparency = 0.15
    title.Parent = frame
    
    local titleCorner = Instance.new("UICorner")
    titleCorner.CornerRadius = UDim.new(0, 16)
    titleCorner.Parent = title
    
    local close = Instance.new("TextButton")
    close.Size = UDim2.new(0, 30, 0, 30)
    close.Position = UDim2.new(1, -38, 0, 7)
    close.Text = "X"
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
    
    local contador = Instance.new("TextLabel")
    contador.Size = UDim2.new(1, 0, 0, 25)
    contador.Position = UDim2.new(0, 0, 1, -35)
    contador.Text = "Total: " .. #usuarios .. " usuario(s)"
    contador.TextColor3 = Color3.fromRGB(200, 200, 200)
    contador.TextSize = 14
    contador.Font = Enum.Font.Gotham
    contador.BackgroundTransparency = 1
    contador.Parent = frame
    
    if #usuarios == 0 then
        local nenhum = Instance.new("TextLabel")
        nenhum.Size = UDim2.new(1, 0, 0, 35)
        nenhum.Text = "Nenhum outro usuario do DAVI HUB encontrado."
        nenhum.TextColor3 = Color3.fromRGB(200, 200, 200)
        nenhum.TextSize = 14
        nenhum.Font = Enum.Font.Gotham
        nenhum.BackgroundTransparency = 1
        nenhum.Parent = lista
    else
        for _, usuario in pairs(usuarios) do
            local item = Instance.new("TextLabel")
            item.Size = UDim2.new(1, 0, 0, 30)
            item.Text = usuario.nome
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
    
    task.wait(0.1)
    lista.CanvasSize = UDim2.new(0, 0, 0, #usuarios * 34 + 50)
end

game:GetService("Players").LocalPlayer.Chatted:Connect(function(msg)
    if msg:lower() == "/davi" or msg:lower() == "/hub" then
        criarGUIUsuarios()
        mostrarUsuariosConsole()
    end
end)

-- ============================================================
-- TAG LARANJA NO CHAT
-- ============================================================

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

local function hookChat()
    local chatService = game:GetService("Chat")
    if chatService and chatService:FindFirstChild("ChatWindow") then
        local chatWindow = chatService.ChatWindow
        local function onMessagePosted(messageData)
            if not messageData or not messageData.FromSpeaker then return end
            local jogador = game.Players:FindFirstChild(messageData.FromSpeaker)
            if jogador and isDaviUser(jogador) then
                messageData.Message = "<font color='#FF8C00'>[DAVI]</font> " .. messageData.Message
            end
            return messageData
        end
        local originalFunction = chatWindow.AddMessageToChannel
        if originalFunction then
            chatWindow.AddMessageToChannel = function(self, messageData, channel)
                messageData = onMessagePosted(messageData) or messageData
                return originalFunction(self, messageData, channel)
            end
        end
    end
end
pcall(hookChat)
print("✅ Chat com tag laranja ativado!")

-- ============================================================
-- SISTEMA DE NOTIFICAÇÕES
-- ============================================================

local function showNotification(text, cor)
    cor = cor or Color3.fromRGB(50, 50, 50)
    local gui = Instance.new("ScreenGui")
    gui.Name = "Notificacao"
    gui.Parent = player.PlayerGui
    gui.ResetOnSpawn = false
    gui.IgnoreGuiInset = true
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 350, 0, 45)
    frame.Position = UDim2.new(1, -370, 0.05, 0)
    frame.BackgroundColor3 = cor
    frame.BackgroundTransparency = 0.15
    frame.BorderSizePixel = 0
    frame.Parent = gui
    local frameCorner = Instance.new("UICorner")
    frameCorner.CornerRadius = UDim.new(0, 12)
    frameCorner.Parent = frame
    local border = Instance.new("UIStroke")
    border.Color = Color3.fromRGB(255, 140, 0)
    border.Thickness = 1
    border.Transparency = 0.3
    border.Parent = frame
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -20, 1, 0)
    label.Position = UDim2.new(0, 10, 0, 0)
    label.Text = text
    label.TextColor3 = Color3.fromRGB(255, 255, 255)
    label.TextSize = 14
    label.Font = Enum.Font.Gotham
    label.BackgroundTransparency = 1
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = frame
    local fadeOut = TweenService:Create(frame, TweenInfo.new(0.5, Enum.EasingStyle.Linear), {BackgroundTransparency = 1})
    local fadeLabel = TweenService:Create(label, TweenInfo.new(0.5, Enum.EasingStyle.Linear), {TextTransparency = 1})
    task.wait(3)
    fadeOut:Play()
    fadeLabel:Play()
    task.wait(0.5)
    gui:Destroy()
end

-- ============================================================
-- NOTIFICADOR (TOGGLE ON/OFF)
-- ============================================================

local notifierActive = false
local notifierConnections = {}

local function iniciarNotificador()
    if notifierActive then return end
    notifierActive = true
    print("🔔 Notificador ATIVADO!")
    local function onChildAdded(child)
        if not notifierActive then return end
        if child.Name == "Larry" then
            showNotification("Larry apareceu!", Color3.fromRGB(200, 50, 50))
        elseif child.Name == "Mutant" then
            showNotification("Mutant apareceu!", Color3.fromRGB(150, 50, 200))
        elseif child.Name == "Worker" then
            showNotification("Worker apareceu!", Color3.fromRGB(50, 150, 200))
        elseif child.Name == "WorkerHead" then
            showNotification("WorkerHead apareceu!", Color3.fromRGB(50, 200, 150))
        end
    end
    local function onChildRemoved(child)
        if not notifierActive then return end
        if child.Name == "Larry" then
            showNotification("Larry desapareceu!", Color3.fromRGB(50, 200, 50))
        elseif child.Name == "Mutant" then
            showNotification("Mutant desapareceu!", Color3.fromRGB(50, 200, 50))
        elseif child.Name == "Worker" then
            showNotification("Worker desapareceu!", Color3.fromRGB(50, 200, 50))
        elseif child.Name == "WorkerHead" then
            showNotification("WorkerHead desapareceu!", Color3.fromRGB(50, 200, 50))
        end
    end
    local conn1 = workspace.ChildAdded:Connect(onChildAdded)
    local conn2 = workspace.ChildRemoved:Connect(onChildRemoved)
    table.insert(notifierConnections, conn1)
    table.insert(notifierConnections, conn2)
    task.wait(1)
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("Model") then
            if obj.Name == "Larry" then
                showNotification("Larry esta aqui!", Color3.fromRGB(200, 50, 50))
            elseif obj.Name == "Mutant" then
                showNotification("Mutant esta aqui!", Color3.fromRGB(150, 50, 200))
            elseif obj.Name == "Worker" then
                showNotification("Worker esta aqui!", Color3.fromRGB(50, 150, 200))
            elseif obj.Name == "WorkerHead" then
                showNotification("WorkerHead esta aqui!", Color3.fromRGB(50, 200, 150))
            end
        end
    end
end

local function desativarNotificador()
    if not notifierActive then return end
    notifierActive = false
    for _, conn in pairs(notifierConnections) do
        pcall(function() conn:Disconnect() end)
    end
    notifierConnections = {}
    print("🔕 Notificador DESATIVADO!")
end
-- ============================================================
-- GUI DE ATIVAÇÃO
-- ============================================================

local function criarGUIAtivacao()
    for _, v in pairs(player.PlayerGui:GetChildren()) do
        if v.Name == "KeySystem" then v:Destroy() end
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
    title.Text = "DISTRIBUICAO DE KEYS"
    title.TextColor3 = Color3.fromRGB(255, 255, 255)
    title.TextSize = 18
    title.Font = Enum.Font.GothamBold
    title.BackgroundTransparency = 1
    title.TextXAlignment = Enum.TextXAlignment.Left
    title.Parent = header

    local minBtn = Instance.new("TextButton")
    minBtn.Size = UDim2.new(0, 30, 0, 30)
    minBtn.Position = UDim2.new(1, -70, 0, 7.5)
    minBtn.Text = "-"
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
        minBtn.Text = minimized and "+" or "-"
        for _, child in pairs(frame:GetChildren()) do
            if child ~= header then
                child.Visible = not minimized
            end
        end
    end)

    local closeBtn = Instance.new("TextButton")
    closeBtn.Size = UDim2.new(0, 30, 0, 30)
    closeBtn.Position = UDim2.new(1, -38, 0, 7.5)
    closeBtn.Text = "X"
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
    end)

    local conteudoFrame = Instance.new("Frame")
    conteudoFrame.Name = "Conteudo"
    conteudoFrame.Size = UDim2.new(1, 0, 1, -45)
    conteudoFrame.Position = UDim2.new(0, 0, 0, 45)
    conteudoFrame.BackgroundTransparency = 1
    conteudoFrame.Parent = frame

    local LINKS_KEYS = {
        ["free_10182alapapqaoqkfa"] = "https://link-target.net/5450045/U8UGMFUQ22Uc",
        ["free_20394blbqbqbrbrlsb"] = "https://direct-link.net/5450045/QykQLu41Fp2x",
        ["free_30567cmcrcrcscsmtc"] = "https://direct-link.net/5450045/m1aASokV7pIF",
        ["free_40821dndsdtdudunud"] = "https://direct-link.net/5450045/glVLfwhKt4et",
        ["free_50943eoeuevevevove"] = "https://link-center.net/5450045/l1peI74weA4g",
        ["free_61054fpfwfwfwfwpwf"] = "https://direct-link.net/5450045/eaCiSMyybd74",
        ["free_71265gqgxgxgxgxqgx"] = "https://link-hub.net/5450045/TGIbYBsV7EcU",
        ["free_81376hrhyhyhyhyrhy"] = "https://link-hub.net/5450045/IkJsx7RSCwyp",
        ["free_91487isizizizizsiz"] = "https://link-center.net/5450045/YR4NQ7ewNSkJ",
        ["free_101598jtjajajajataj"] = "https://link-target.net/5450045/gdChsmYq0rb5",
    }

    local function getProximaKeyDisponivel()
        for key, link in pairs(LINKS_KEYS) do
            return key, link
        end
        return nil, nil
    end

    local function contarKeysDisponiveis()
        local sucesso, resultado = pcall(function()
            return game:HttpGet(PASTEBIN_URL)
        end)
        if sucesso and resultado then
            local count = 0
            for key in string.gmatch(resultado, "%S+") do
                if key ~= "" then count = count + 1 end
            end
            return count
        else
            return "?"
        end
    end

    local totalKeys = contarKeysDisponiveis()
    local sub = Instance.new("TextLabel")
    sub.Size = UDim2.new(1, 0, 0, 25)
    sub.Position = UDim2.new(0, 0, 0.05, 0)
    sub.Text = "Digite sua key de ativacao  |  " .. totalKeys .. " keys disponiveis"
    sub.TextColor3 = Color3.fromRGB(200, 200, 200)
    sub.TextSize = 13
    sub.Font = Enum.Font.Gotham
    sub.BackgroundTransparency = 1
    sub.Parent = conteudoFrame

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
    keyBox.Parent = conteudoFrame
    local keyCorner = Instance.new("UICorner")
    keyCorner.CornerRadius = UDim.new(0, 10)
    keyCorner.Parent = keyBox

    local btnAtivar = Instance.new("TextButton")
    btnAtivar.Size = UDim2.new(0.35, 0, 0, 45)
    btnAtivar.Position = UDim2.new(0.1, 0, 0.30, 0)
    btnAtivar.Text = "ATIVAR"
    btnAtivar.TextColor3 = Color3.fromRGB(255, 255, 255)
    btnAtivar.TextSize = 18
    btnAtivar.Font = Enum.Font.GothamBold
    btnAtivar.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
    btnAtivar.BackgroundTransparency = 0.15
    btnAtivar.BorderSizePixel = 0
    btnAtivar.Parent = conteudoFrame
    local btnCorner = Instance.new("UICorner")
    btnCorner.CornerRadius = UDim.new(0, 10)
    btnCorner.Parent = btnAtivar

    local btnGetKey = Instance.new("TextButton")
    btnGetKey.Size = UDim2.new(0.35, 0, 0, 45)
    btnGetKey.Position = UDim2.new(0.55, 0, 0.30, 0)
    btnGetKey.Text = "OBTER KEY"
    btnGetKey.TextColor3 = Color3.fromRGB(255, 255, 255)
    btnGetKey.TextSize = 16
    btnGetKey.Font = Enum.Font.GothamBold
    btnGetKey.BackgroundColor3 = Color3.fromRGB(45, 150, 200)
    btnGetKey.BackgroundTransparency = 0.15
    btnGetKey.BorderSizePixel = 0
    btnGetKey.Parent = conteudoFrame
    local btnGetKeyCorner = Instance.new("UICorner")
    btnGetKeyCorner.CornerRadius = UDim.new(0, 10)
    btnGetKeyCorner.Parent = btnGetKey

    btnGetKey.MouseButton1Click:Connect(function()
        local key, link = getProximaKeyDisponivel()
        if link then
            print("🔗 PROXIMA KEY: " .. key)
            print("🔗 Link: " .. link)
            pcall(function() setclipboard(link) end)
        else
            print("❌ Nenhuma key disponivel!")
        end
    end)

    local status = Instance.new("TextLabel")
    status.Size = UDim2.new(1, 0, 0, 25)
    status.Position = UDim2.new(0, 0, 0.45, 0)
    status.Text = "Digite sua key e clique em ATIVAR"
    status.TextColor3 = Color3.fromRGB(150, 150, 150)
    status.TextSize = 13
    status.Font = Enum.Font.Gotham
    status.BackgroundTransparency = 1
    status.Parent = conteudoFrame

    btnAtivar.MouseButton1Click:Connect(function()
        local key = keyBox.Text
        if key == "" then
            status.Text = "Digite uma key!"
            status.TextColor3 = Color3.fromRGB(255, 100, 100)
            return
        end
        local valida, mensagem = verificarKey(key)
        if valida then
            status.Text = "KEY VALIDA! Ativando DAVI HUB..."
            status.TextColor3 = Color3.fromRGB(100, 255, 100)
            btnAtivar.Text = "ATIVADO!"
            btnAtivar.BackgroundColor3 = Color3.fromRGB(0, 200, 0)
            player:SetAttribute("DAVI_KEY", key)
            keyValidada = true
            task.wait(1)
            gui:Destroy()
            print("✅ DAVI HUB ATIVADO!")
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
            status.Text = valida and "KEY VALIDA! Clique em ATIVAR." or mensagem
            status.TextColor3 = valida and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
        end
    end)

    return gui
end

-- ============================================================
-- VERIFICAÇÃO INICIAL
-- ============================================================

if verificarKeySalva() then
    print("✅ DAVI HUB ja esta ativado!")
    keyValidada = true
else
    print("🔑 Aguardando ativacao da key...")
    criarGUIAtivacao()
    while not keyValidada do
        task.wait(0.5)
        local guiExists = false
        for _, v in pairs(player.PlayerGui:GetChildren()) do
            if v.Name == "KeySystem" then
                guiExists = true
                break
            end
        end
        if not guiExists and not keyValidada then
            print("❌ Ativacao cancelada.")
            return
        end
    end
end

print(" Carregando DAVI HUB...")
-- ============================================================
-- CRIA GUI PRINCIPAL
-- ============================================================
local gui = Instance.new("ScreenGui")
gui.Name = "DaviHub"
gui.Parent = player:WaitForChild("PlayerGui")
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true

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

-- BOTÃO EMBAIXO DA GUI (ARRASTÁVEL)
local moverBtn = Instance.new("TextButton")
moverBtn.Name = "MoverBtn"
moverBtn.Size = UDim2.new(0.4, 0, 0, 25)
moverBtn.Position = UDim2.new(0.3, 0, 1, -10)
moverBtn.BackgroundColor3 = Color3.fromRGB(180, 180, 180)
moverBtn.BackgroundTransparency = 0.3
moverBtn.Text = "==="
moverBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
moverBtn.TextSize = 14
moverBtn.Font = Enum.Font.GothamBold
moverBtn.BorderSizePixel = 0
moverBtn.ZIndex = 10
moverBtn.Parent = mainFrame

local btnCorner = Instance.new("UICorner")
btnCorner.CornerRadius = UDim.new(0, 10)
btnCorner.Parent = moverBtn

local moverDragging = false
local moverDragStart, moverStartPos
local moverDragInput

moverBtn.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        moverDragging = true
        moverDragStart = input.Position
        moverStartPos = mainFrame.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                moverDragging = false
            end
        end)
    end
end)

moverBtn.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        moverDragInput = input
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if moverDragging and input == moverDragInput then
        local delta = input.Position - moverDragStart
        mainFrame.Position = UDim2.new(
            moverStartPos.X.Scale,
            moverStartPos.X.Offset + delta.X,
            moverStartPos.Y.Scale,
            moverStartPos.Y.Offset + delta.Y
        )
    end
end)

-- ============================================================
-- CABEÇALHO
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
titulo.Text = "DAVI HUB"
titulo.TextColor3 = Color3.fromRGB(255, 255, 255)
titulo.TextSize = 18
titulo.Font = Enum.Font.GothamBold
titulo.BackgroundTransparency = 1
titulo.TextXAlignment = Enum.TextXAlignment.Left
titulo.Parent = header

local minBtn = Instance.new("TextButton")
minBtn.Size = UDim2.new(0, 32, 0, 32)
minBtn.Position = UDim2.new(1, -70, 0, 6.5)
minBtn.Text = "-"
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
    minBtn.Text = minimized and "+" or "-"
    for _, child in pairs(mainFrame:GetChildren()) do
        if child ~= header then
            child.Visible = not minimized
        end
    end
end)

local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 32, 0, 32)
closeBtn.Position = UDim2.new(1, -38, 0, 6.5)
closeBtn.Text = "X"
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
-- CONTEÚDO E ABAS
-- ============================================================
local contentFrame = Instance.new("Frame")
contentFrame.Size = UDim2.new(1, 0, 1, -45)
contentFrame.Position = UDim2.new(0, 0, 0, 45)
contentFrame.BackgroundTransparency = 1
contentFrame.Parent = mainFrame

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

local abaButtons = {}
local abaFrames = {}
local abas = {
    {name = t("main"), label = "Main"},
    {name = t("menu"), label = "Menu"},
    {name = t("tele"), label = "Tele"},
    {name = t("n1"), label = "N1"},
    {name = t("n2"), label = "N2"},
    {name = t("n3"), label = "N3"},
    {name = t("esp"), label = "ESP"},
    {name = t("geral"), label = "Geral"},
    {name = "⚙️", label = "Config"},
}

local function criarAba(nome, icone)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.85, 0, 0, 45)
    btn.Position = UDim2.new(0.075, 0, 0, 0)
    btn.Text = nome
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
        if configs.autoload then salvarConfiguracoes() end
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

    local draggingSlider = false
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
        if configs.autoload then salvarConfiguracoes() end
    end

    sb.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            draggingSlider = true
            update(input)
        end
    end)
    sb.InputEnded:Connect(function() draggingSlider = false end)
    UserInputService.InputChanged:Connect(function(input)
        if draggingSlider and input.UserInputType == Enum.UserInputType.MouseMovement then
            update(input)
        end
    end)
end
-- ============================================================
-- FUNÇÕES DO JOGO
-- ============================================================

local noclipLoop = nil
local function toggleNoclip(v)
    if noclipLoop then noclipLoop:Disconnect(); noclipLoop = nil end
    if v then
        noclipLoop = RunService.Stepped:Connect(function()
            local char = player.Character
            if char then
                for _, part in pairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then part.CanCollide = false end
                end
            end
        end)
    else
        local char = player.Character
        if char then
            for _, part in pairs(char:GetDescendants()) do
                if part:IsA("BasePart") then part.CanCollide = true end
            end
        end
    end
end

local origLight = {}
local function toggleFullbright(v)
    local li = Lighting
    if v then
        origLight = {Ambient = li.Ambient, OutdoorAmbient = li.OutdoorAmbient, Brightness = li.Brightness, GlobalShadows = li.GlobalShadows, FogEnd = li.FogEnd}
        li.Ambient = Color3.fromRGB(255,255,255)
        li.OutdoorAmbient = Color3.fromRGB(255,255,255)
        li.Brightness = 2
        li.GlobalShadows = false
        li.FogEnd = 100000
    else
        li.Ambient = origLight.Ambient or Color3.fromRGB(128,128,128)
        li.OutdoorAmbient = origLight.OutdoorAmbient or Color3.fromRGB(128,128,128)
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
                if breath then breath:SetAttribute("Max",999999); breath.Value = 999999 end
                local blur = Lighting:FindFirstChild("Blur")
                if blur then blur.Enabled = false end
            end
        end)
    end
end

-- ============================================================
-- TELEPORTS
-- ============================================================
local teleportsN1 = {
    ["Ladder"] = CFrame.new(-0.173,9.3,-81.32),
    ["Generator"] = CFrame.new(-79.722,4.675,-131.918),
    ["Entrance"] = CFrame.new(-11.036,7.73,-31.822),
    ["LivingRoom"] = CFrame.new(-34.962,8.05,-47.153),
    ["Bedroom"] = CFrame.new(-32.645,23.8,-72.845),
}
local teleportsN2 = {
    ["SafeSpot N2"] = CFrame.new(-339.321,82.4,-40.622),
    ["DeliveryBoard"] = CFrame.new(-282.224,82.4,14.674),
    ["Main"] = CFrame.new(-304.235,82.4,-6.777),
    ["Corridor1"] = CFrame.new(-303.846,82.4,50.169),
    ["Entrance2"] = CFrame.new(-217.417,82.4,65.412),
    ["Corridor2"] = CFrame.new(-293.11,82.4,-89.501),
}
local teleportsN3 = {
    ["Cabana 1"] = CFrame.new(99.8,4.5,-247.2),
    ["Cabana 2"] = CFrame.new(-36.9,4.5,68.7),
    ["Cabana 3"] = CFrame.new(-31.7,4.5,268.8),
    ["Cabana 4"] = CFrame.new(233.6,4.5,245.8),
    ["Cutscene Room"] = CFrame.new(-237,-22.5,107),
    ["Safe Spot N3"] = CFrame.new(194,38.7,-217.4),
    ["Lodge"] = CFrame.new(-226.8,17.4,103.7),
    ["Jeffry Canna"] = CFrame.new(177.5,4.3,197.9),
}

local function teleportar(cframe)
    local char = player.Character or player.CharacterAdded:Wait()
    if char and char:FindFirstChild("HumanoidRootPart") then
        char.HumanoidRootPart.CFrame = cframe
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
        char.HumanoidRootPart.CFrame = CFrame.new(-27.149,8.7,-118.611)
        task.wait(0.25)
        local cl = d and d:FindFirstChild("ClickDetector")
        if cl then fireclickdetector(cl) end
        task.wait(0.25)
        char.HumanoidRootPart.CFrame = CFrame.new(-45.113,7.849,-60.241)
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
        char.HumanoidRootPart.CFrame = CFrame.new(-27.149,8.7,-118.611)
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
    char.HumanoidRootPart.CFrame = CFrame.new(-76.039,4.675,-133.78)
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
    char.HumanoidRootPart.CFrame = CFrame.new(-76.039,4.675,-133.78)
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
-- AIMBOT
-- ============================================================
local NPC_NAMES = {"Worker","ModelWorker","WorkerHead","Mutant","Larry","Stalker","Spider"}
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
local function toggleESPPlayers(v)
    for _, h in pairs(espPlayers) do if h then h:Destroy() end end
    espPlayers = {}
    if v then
        for _, p in pairs(game.Players:GetPlayers()) do
            if p ~= player and p.Character then
                local h = Instance.new("Highlight")
                h.FillColor = Color3.fromRGB(0,255,0)
                h.OutlineColor = Color3.fromRGB(0,150,0)
                h.FillTransparency = 0.5
                h.Adornee = p.Character
                h.Parent = p.Character
                espPlayers[p] = h
            end
        end
    end
end

local espLarryAtivo = false
local espLarryHighlight = nil
local function toggleESPLarry(v)
    if espLarryHighlight then espLarryHighlight:Destroy(); espLarryHighlight = nil end
    espLarryAtivo = v
    if v then
        local function adicionarLarry()
            local larry = workspace:FindFirstChild("Larry")
            if larry then
                if espLarryHighlight then espLarryHighlight:Destroy() end
                espLarryHighlight = Instance.new("Highlight")
                espLarryHighlight.Parent = larry
                espLarryHighlight.Adornee = larry
                espLarryHighlight.FillColor = Color3.fromRGB(255,0,0)
                espLarryHighlight.OutlineColor = Color3.fromRGB(255,255,255)
                espLarryHighlight.FillTransparency = 0.4
            end
        end
        adicionarLarry()
        workspace.ChildAdded:Connect(function(child)
            if espLarryAtivo and child.Name == "Larry" then
                task.wait(1); adicionarLarry()
            end
        end)
    end
end

local espStalkerAtivo = false
local espStalkerHighlight = nil
local function toggleESPStalker(v)
    if espStalkerHighlight then espStalkerHighlight:Destroy(); espStalkerHighlight = nil end
    espStalkerAtivo = v
    if v then
        local function adicionarStalker()
            local stalker = workspace:FindFirstChild("Stalker")
            if stalker then
                if espStalkerHighlight then espStalkerHighlight:Destroy() end
                espStalkerHighlight = Instance.new("Highlight")
                espStalkerHighlight.Parent = stalker
                espStalkerHighlight.Adornee = stalker
                espStalkerHighlight.FillColor = Color3.fromRGB(255,165,0)
                espStalkerHighlight.OutlineColor = Color3.fromRGB(255,255,255)
                espStalkerHighlight.FillTransparency = 0.4
            end
        end
        adicionarStalker()
        workspace.ChildAdded:Connect(function(child)
            if espStalkerAtivo and child.Name == "Stalker" then
                task.wait(1); adicionarStalker()
            end
        end)
    end
end

local espZombieAtivo = false
local espZombieHighlights = {}
local function toggleESPZombie(v)
    for _, h in pairs(espZombieHighlights) do if h then h:Destroy() end end
    espZombieHighlights = {}
    espZombieAtivo = v
    if v then
        local function adicionarZombies()
            for _, h in pairs(espZombieHighlights) do if h then h:Destroy() end end
            espZombieHighlights = {}
            for _, obj in pairs(workspace:GetDescendants()) do
                if obj:IsA("Model") and (obj.Name == "Zombie" or obj.Name == "Skeleton") then
                    local highlight = Instance.new("Highlight")
                    highlight.Parent = obj
                    highlight.Adornee = obj
                    highlight.FillColor = Color3.fromRGB(0,255,0)
                    highlight.OutlineColor = Color3.fromRGB(255,255,255)
                    highlight.FillTransparency = 0.4
                    table.insert(espZombieHighlights, highlight)
                end
            end
        end
        adicionarZombies()
        workspace.ChildAdded:Connect(function(child)
            if espZombieAtivo and (child.Name == "Zombie" or child.Name == "Skeleton") then
                task.wait(0.5); adicionarZombies()
            end
        end)
    end
end

-- ============================================================
-- SISTEMA DE FEEDBACK
-- ============================================================

local CATEGORIAS_FEEDBACK = {"Bug Report","Sugestao","Elogio","Critica","Duvida","Nova Funcao","Performance","Tutorial","Suporte","Abuso/Report","Estatisticas","Experiencia"}

local function enviarFeedback(usuario, mensagem, categoria)
    local data = {["embeds"] = {{
        ["title"] = "NOVO FEEDBACK - DAVI HUB",
        ["color"] = 16753920,
        ["fields"] = {
            {["name"] = "Usuario", ["value"] = usuario or "Desconhecido", ["inline"] = true},
            {["name"] = "Categoria", ["value"] = categoria or "Geral", ["inline"] = true},
            {["name"] = "Mensagem", ["value"] = mensagem or "Sem mensagem", ["inline"] = false},
            {["name"] = "UserID", ["value"] = tostring(player.UserId), ["inline"] = true},
            {["name"] = "Servidor", ["value"] = game.JobId or "N/A", ["inline"] = true},
            {["name"] = "Data", ["value"] = os.date("%d/%m/%Y %H:%M:%S"), ["inline"] = true},
        },
        ["footer"] = {["text"] = "DAVI HUB - Feedback"},
        ["timestamp"] = os.date("!%Y-%m-%dT%H:%M:%S.000Z")
    }}}
    local json = HttpService:JSONEncode(data)
    pcall(function()
        local request = syn and syn.request or request or http_request
        if request then
            request({Url = WEBHOOK_URL, Method = "POST", Headers = {["Content-Type"] = "application/json"}, Body = json})
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
    fundo.Size = UDim2.new(1,0,1,0)
    fundo.BackgroundColor3 = Color3.fromRGB(0,0,0)
    fundo.BackgroundTransparency = 0.5
    fundo.Parent = gui
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0,450,0,380)
    frame.Position = UDim2.new(0.5,-225,0.5,-190)
    frame.BackgroundColor3 = Color3.fromRGB(20,20,35)
    frame.BackgroundTransparency = 0.05
    frame.BorderSizePixel = 0
    frame.ClipsDescendants = true
    frame.Parent = fundo
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0,16)
    corner.Parent = frame
    local border = Instance.new("UIStroke")
    border.Color = Color3.fromRGB(255,140,0)
    border.Thickness = 2
    border.Transparency = 0.3
    border.Parent = frame
    local title = Instance.new("TextLabel")
    title.Size = UDim2.new(1,0,0,45)
    title.Text = "ENVIAR FEEDBACK"
    title.TextColor3 = Color3.fromRGB(255,200,50)
    title.TextSize = 20
    title.Font = Enum.Font.GothamBold
    title.BackgroundColor3 = Color3.fromRGB(255,140,0)
    title.BackgroundTransparency = 0.15
    title.Parent = frame
    local titleCorner = Instance.new("UICorner")
    titleCorner.CornerRadius = UDim.new(0,16)
    titleCorner.Parent = title
    local close = Instance.new("TextButton")
    close.Size = UDim2.new(0,30,0,30)
    close.Position = UDim2.new(1,-38,0,7)
    close.Text = "X"
    close.TextColor3 = Color3.fromRGB(255,255,255)
    close.TextSize = 18
    close.BackgroundColor3 = Color3.fromRGB(200,40,40)
    close.BackgroundTransparency = 0.3
    close.BorderSizePixel = 0
    close.Parent = title
    local closeCorner = Instance.new("UICorner")
    closeCorner.CornerRadius = UDim.new(0,8)
    closeCorner.Parent = close
    close.MouseButton1Click:Connect(function() gui:Destroy() end)
    local labelCategoria = Instance.new("TextLabel")
    labelCategoria.Size = UDim2.new(0.35,0,0,25)
    labelCategoria.Position = UDim2.new(0.05,0,0.15,0)
    labelCategoria.Text = "Categoria:"
    labelCategoria.TextColor3 = Color3.fromRGB(200,200,200)
    labelCategoria.TextSize = 14
    labelCategoria.Font = Enum.Font.GothamBold
    labelCategoria.BackgroundTransparency = 1
    labelCategoria.TextXAlignment = Enum.TextXAlignment.Left
    labelCategoria.Parent = frame
    local categoriaAtual = 1
    local dropdownVisible = false
    local dropBtn = Instance.new("TextButton")
    dropBtn.Size = UDim2.new(0.50,0,0,30)
    dropBtn.Position = UDim2.new(0.45,0,0.15,0)
    dropBtn.Text = CATEGORIAS_FEEDBACK[1]
    dropBtn.TextColor3 = Color3.fromRGB(255,255,255)
    dropBtn.TextSize = 13
    dropBtn.Font = Enum.Font.GothamBold
    dropBtn.BackgroundColor3 = Color3.fromRGB(45,45,60)
    dropBtn.BackgroundTransparency = 0.2
    dropBtn.BorderSizePixel = 0
    dropBtn.Parent = frame
    local dropCorner = Instance.new("UICorner")
    dropCorner.CornerRadius = UDim.new(0,8)
    dropCorner.Parent = dropBtn
    local dropMenu = Instance.new("ScrollingFrame")
    dropMenu.Size = UDim2.new(0.50,0,0,0)
    dropMenu.Position = UDim2.new(0.45,0,0.22,0)
    dropMenu.BackgroundColor3 = Color3.fromRGB(30,30,45)
    dropMenu.BackgroundTransparency = 0.1
    dropMenu.BorderSizePixel = 0
    dropMenu.ClipsDescendants = true
    dropMenu.Visible = false
    dropMenu.ScrollBarThickness = 4
    dropMenu.Parent = frame
    local dropCorner2 = Instance.new("UICorner")
    dropCorner2.CornerRadius = UDim.new(0,8)
    dropCorner2.Parent = dropMenu
    local dropLayout = Instance.new("UIListLayout")
    dropLayout.Padding = UDim.new(0,2)
    dropLayout.SortOrder = Enum.SortOrder.LayoutOrder
    dropLayout.Parent = dropMenu
    for i, cat in pairs(CATEGORIAS_FEEDBACK) do
        local opt = Instance.new("TextButton")
        opt.Size = UDim2.new(1,0,0,30)
        opt.Text = cat
        opt.TextColor3 = Color3.fromRGB(255,255,255)
        opt.TextSize = 13
        opt.Font = Enum.Font.Gotham
        opt.BackgroundColor3 = Color3.fromRGB(45,45,60)
        opt.BackgroundTransparency = 0.2
        opt.BorderSizePixel = 0
        opt.Parent = dropMenu
        local optCorner = Instance.new("UICorner")
        optCorner.CornerRadius = UDim.new(0,6)
        optCorner.Parent = opt
        opt.MouseButton1Click:Connect(function()
            categoriaAtual = i
            dropBtn.Text = CATEGORIAS_FEEDBACK[i]
            dropdownVisible = false
            dropMenu.Visible = false
            dropMenu.Size = UDim2.new(0.50,0,0,0)
        end)
    end
    dropBtn.MouseButton1Click:Connect(function()
        dropdownVisible = not dropdownVisible
        dropMenu.Visible = dropdownVisible
        if dropdownVisible then
            local totalHeight = #CATEGORIAS_FEEDBACK * 32
            local maxHeight = 150
            dropMenu.Size = UDim2.new(0.50,0,0,math.min(totalHeight,maxHeight))
            dropMenu.CanvasSize = UDim2.new(0,0,0,totalHeight)
        else
            dropMenu.Size = UDim2.new(0.50,0,0,0)
        end
    end)
    local labelMsg = Instance.new("TextLabel")
    labelMsg.Size = UDim2.new(0.9,0,0,25)
    labelMsg.Position = UDim2.new(0.05,0,0.30,0)
    labelMsg.Text = "Mensagem:"
    labelMsg.TextColor3 = Color3.fromRGB(200,200,200)
    labelMsg.TextSize = 14
    labelMsg.Font = Enum.Font.GothamBold
    labelMsg.BackgroundTransparency = 1
    labelMsg.TextXAlignment = Enum.TextXAlignment.Left
    labelMsg.Parent = frame
    local msgBox = Instance.new("TextBox")
    msgBox.Size = UDim2.new(0.9,0,0,100)
    msgBox.Position = UDim2.new(0.05,0,0.37,0)
    msgBox.BackgroundColor3 = Color3.fromRGB(35,35,50)
    msgBox.BackgroundTransparency = 0.2
    msgBox.TextColor3 = Color3.fromRGB(255,255,255)
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
    msgCorner.CornerRadius = UDim.new(0,8)
    msgCorner.Parent = msgBox
    local btnEnviar = Instance.new("TextButton")
    btnEnviar.Size = UDim2.new(0.4,0,0,40)
    btnEnviar.Position = UDim2.new(0.05,0,0.72,0)
    btnEnviar.Text = "Enviar"
    btnEnviar.TextColor3 = Color3.fromRGB(255,255,255)
    btnEnviar.TextSize = 16
    btnEnviar.Font = Enum.Font.GothamBold
    btnEnviar.BackgroundColor3 = Color3.fromRGB(255,140,0)
    btnEnviar.BackgroundTransparency = 0.2
    btnEnviar.BorderSizePixel = 0
    btnEnviar.Parent = frame
    local btnCorner = Instance.new("UICorner")
    btnCorner.CornerRadius = UDim.new(0,8)
    btnCorner.Parent = btnEnviar
    local btnCancelar = Instance.new("TextButton")
    btnCancelar.Size = UDim2.new(0.4,0,0,40)
    btnCancelar.Position = UDim2.new(0.55,0,0.72,0)
    btnCancelar.Text = "Cancelar"
    btnCancelar.TextColor3 = Color3.fromRGB(255,255,255)
    btnCancelar.TextSize = 16
    btnCancelar.Font = Enum.Font.GothamBold
    btnCancelar.BackgroundColor3 = Color3.fromRGB(200,40,40)
    btnCancelar.BackgroundTransparency = 0.2
    btnCancelar.BorderSizePixel = 0
    btnCancelar.Parent = frame
    local btnCancelarCorner = Instance.new("UICorner")
    btnCancelarCorner.CornerRadius = UDim.new(0,8)
    btnCancelarCorner.Parent = btnCancelar
    btnCancelar.MouseButton1Click:Connect(function() gui:Destroy() end)
    local statusLabel = Instance.new("TextLabel")
    statusLabel.Size = UDim2.new(0.9,0,0,25)
    statusLabel.Position = UDim2.new(0.05,0,0.86,0)
    statusLabel.Text = "Selecione uma categoria e escreva sua mensagem!"
    statusLabel.TextColor3 = Color3.fromRGB(100,255,100)
    statusLabel.TextSize = 13
    statusLabel.Font = Enum.Font.Gotham
    statusLabel.BackgroundTransparency = 1
    statusLabel.Parent = frame
    btnEnviar.MouseButton1Click:Connect(function()
        local mensagem = msgBox.Text
        if mensagem == "" or mensagem == "Escreva sua mensagem aqui..." then
            statusLabel.Text = "Digite uma mensagem!"
            statusLabel.TextColor3 = Color3.fromRGB(255,100,100)
            return
        end
        local categoria = CATEGORIAS_FEEDBACK[categoriaAtual]
        statusLabel.Text = "Enviando..."
        statusLabel.TextColor3 = Color3.fromRGB(255,255,100)
        enviarFeedback(player.Name, mensagem, categoria)
        statusLabel.Text = "Feedback enviado! Obrigado!"
        statusLabel.TextColor3 = Color3.fromRGB(100,255,100)
        msgBox.Text = ""
        btnEnviar.Text = "Enviado!"
        btnEnviar.BackgroundColor3 = Color3.fromRGB(0,200,0)
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
-- CONSTRUÇÃO DAS ABAS
-- ============================================================

-- ABA: MAIN
local mainAba = abaFramesMap["Main"]
local mainCard = addCard(mainAba)
addLabel(mainCard, t("main"))
addToggle(mainCard, t("fullbright"), function(v) toggleFullbright(v); configs.fullbright = v end, configs.fullbright)
addToggle(mainCard, t("stamina"), function(v) toggleStamina(v); configs.stamina = v end, configs.stamina)
addToggle(mainCard, t("antifrost"), function(v) toggleAntiTemp(v); configs.antifrost = v end, configs.antifrost)
addToggle(mainCard, t("o2"), function(v) toggleO2(v); configs.o2 = v end, configs.o2)
addToggle(mainCard, t("noclip"), function(v) toggleNoclip(v); configs.noclip = v end, configs.noclip)
addSlider(mainCard, t("sprint"), function(v)
    local ch = player.Character
    if ch then
        ch:SetAttribute("SprintSpeed", v)
        local hum = ch:FindFirstChild("Humanoid")
        if hum then hum.WalkSpeed = v end
    end
    configs.sprint_speed = v
end, 17, 45, configs.sprint_speed)

-- ABA: MENU
local menuFrame = abaFramesMap["Menu"]
local menuCard = addCard(menuFrame)
addLabel(menuCard, t("menu"))
addLabel(menuCard, t("bemvindo"), Color3.fromRGB(255,255,255))
addLabel(menuCard, t("selecione"), Color3.fromRGB(200,200,200))
addLabel(menuCard, t("versao"), Color3.fromRGB(150,150,150))
addButton(menuCard, t("feedback"), abrirFeedback)
addButton(menuCard, t("usuarios"), function() criarGUIUsuarios(); mostrarUsuariosConsole() end)
addButton(menuCard, "🔑 Status da Key", statusKey)

-- ABA: TELEPORTS
local teleFrame = abaFramesMap["Tele"]
local teleCard = addCard(teleFrame)
addLabel(teleCard, t("n1"))
for nome, cf in pairs(teleportsN1) do
    addButton(teleCard, nome, function() teleportar(cf) end)
end
addLabel(teleCard, t("n2"))
for nome, cf in pairs(teleportsN2) do
    addButton(teleCard, nome, function() teleportar(cf) end)
end
addLabel(teleCard, t("n3"))
for nome, cf in pairs(teleportsN3) do
    addButton(teleCard, nome, function() teleportar(cf) end)
end

-- ABA: NIGHT 1
local n1Frame = abaFramesMap["N1"]
local n1Card = addCard(n1Frame)
addLabel(n1Card, t("n1"))
addButton(n1Card, "Refill Fireplace", RefillFireplace)
addButton(n1Card, "Grab Wood", GrabWood)
addButton(n1Card, "Refill Generator", RefillGenerator)
addButton(n1Card, "Grab JerryCan", GrabJerryCan)

-- ABA: NIGHT 2
local n2Frame = abaFramesMap["N2"]
local n2Card = addCard(n2Frame)
addLabel(n2Card, t("n2"))
addToggle(n2Card, t("autoscare"), function(v) scareActive = v; configs.autoscare = v end, configs.autoscare)
addButton(n2Card, t("antivent"), AntiVentPests)
addButton(n2Card, t("revive"), ReviveN2)
addButton(n2Card, t("escapesnatch"), EscapeSnatch)
addButton(n2Card, t("refillpower"), function()
    local char = player.Character or player.CharacterAdded:Wait()
    local OC = char.HumanoidRootPart.CFrame
    char.HumanoidRootPart.CFrame = CFrame.new(-318.606567,82.3999634,97.8991776)
    task.wait(0.25)
    fireclickdetector(workspace.PowerCell.ClickDetector)
    task.wait(0.25)
    char.HumanoidRootPart.CFrame = CFrame.new(-271.368439,82.3999634,112.977608)
    task.wait(0.25)
    fireclickdetector(workspace.Generator.Detector.ClickDetector)
    task.wait(0.25)
    char.HumanoidRootPart.CFrame = OC
end)
addButton(n2Card, t("antistalker"), function()
    local remotes = game.ReplicatedStorage:FindFirstChild("Remotes")
    if remotes and remotes:FindFirstChild("LookAt") then
        remotes.LookAt:Destroy()
        print("✅ Anti Stalker ativado!")
    end
end)

-- ABA: NIGHT 3
local n3Frame = abaFramesMap["N3"]
local n3Card = addCard(n3Frame)
addLabel(n3Card, t("n3"))
addToggle(n3Card, t("aimbot"), function(v) aimbotEnabled = v; configs.aimbot = v end, configs.aimbot)
addSlider(n3Card, t("aimspeed"), function(v) AIM_SPEED = v; configs.aim_speed = v end, 30, 150, configs.aim_speed)
addSlider(n3Card, t("aimdist"), function(v) MAX_DISTANCE = v; configs.aim_distance = v end, 30, 150, configs.aim_distance)
addButton(n3Card, t("municao"), AutoMunicao)

-- ABA: ESP
local espFrame = abaFramesMap["ESP"]
local espCard = addCard(espFrame)
addLabel(espCard, t("esp"))
addToggle(espCard, t("esp_players"), function(v) toggleESPPlayers(v); configs.esp_players = v end, configs.esp_players)
addToggle(espCard, t("esp_larry"), function(v) toggleESPLarry(v); configs.esp_larry = v end, configs.esp_larry)
addToggle(espCard, t("esp_stalker"), function(v) toggleESPStalker(v); configs.esp_stalker = v end, configs.esp_stalker)
addToggle(espCard, t("esp_zombie"), function(v) toggleESPZombie(v); configs.esp_zombie = v end, configs.esp_zombie)

-- ABA: GERAL
local geralFrame = abaFramesMap["Geral"]
local geralCard = addCard(geralFrame)
addLabel(geralCard, t("geral"))
addButton(geralCard, t("bypass"), function()
    local char = player.Character or player.CharacterAdded:Wait()
    local hum = char:FindFirstChild("Humanoid")
    if hum then
        hum:SetAttribute("AntiCheatBypass", true)
        print("✅ Anti-Cheat bypassado!")
    end
end)
addButton(geralCard, t("static"), function()
    local static = workspace:FindFirstChild("Static")
    if static then
        static:Destroy()
        print("✅ Estatica removida!")
    else
        print("❌ Estatica nao encontrada.")
    end
end)
addToggle(geralCard, t("notifier"), function(v)
    if v then iniciarNotificador(); configs.notifier = true else desativarNotificador(); configs.notifier = false end
end, configs.notifier)

-- ABA: CONFIGURACOES
local configFrame = abaFramesMap["Config"]
local configCard = addCard(configFrame)
addLabel(configCard, t("config"))
addButton(configCard, t("salvar"), salvarConfiguracoes)
addButton(configCard, t("carregar"), function() carregarConfiguracoes(); print("📂 Configuracoes carregadas!") end)
addToggle(configCard, t("autoload"), function(v) configs.autoload = v; salvarConfiguracoes() end, configs.autoload)

-- SELETOR DE IDIOMA
local idiomaIndex = 1
for i, v in pairs(listaIdiomas) do
    if v.codigo == idiomaAtual then idiomaIndex = i; break end
end

local idiomaBtn = Instance.new("TextButton")
idiomaBtn.Size = UDim2.new(0.9,0,0,35)
idiomaBtn.Position = UDim2.new(0.05,0,0.42,0)
idiomaBtn.Text = "🌐 " .. t("idioma") .. ": " .. listaIdiomas[idiomaIndex].nome
idiomaBtn.TextColor3 = Color3.fromRGB(255,255,255)
idiomaBtn.TextSize = 13
idiomaBtn.Font = Enum.Font.GothamBold
idiomaBtn.BackgroundColor3 = Color3.fromRGB(45,45,60)
idiomaBtn.BackgroundTransparency = 0.2
idiomaBtn.BorderSizePixel = 0
idiomaBtn.Parent = configCard

local idiomaBtnCorner = Instance.new("UICorner")
idiomaBtnCorner.CornerRadius = UDim.new(0,8)
idiomaBtnCorner.Parent = idiomaBtn

local idiomaMenu = Instance.new("ScrollingFrame")
idiomaMenu.Size = UDim2.new(0.9,0,0,0)
idiomaMenu.Position = UDim2.new(0.05,0,0.50,0)
idiomaMenu.BackgroundColor3 = Color3.fromRGB(30,30,45)
idiomaMenu.BackgroundTransparency = 0.2
idiomaMenu.BorderSizePixel = 0
idiomaMenu.ClipsDescendants = true
idiomaMenu.Visible = false
idiomaMenu.ScrollBarThickness = 4
idiomaMenu.Parent = configCard

local idiomaMenuCorner = Instance.new("UICorner")
idiomaMenuCorner.CornerRadius = UDim.new(0,8)
idiomaMenuCorner.Parent = idiomaMenu

local idiomaListLayout = Instance.new("UIListLayout")
idiomaListLayout.Padding = UDim.new(0,2)
idiomaListLayout.SortOrder = Enum.SortOrder.LayoutOrder
idiomaListLayout.Parent = idiomaMenu

for i, idioma in pairs(listaIdiomas) do
    local opt = Instance.new("TextButton")
    opt.Size = UDim2.new(1,0,0,30)
    opt.Text = idioma.nome
    opt.TextColor3 = Color3.fromRGB(255,255,255)
    opt.TextSize = 13
    opt.Font = Enum.Font.Gotham
    opt.BackgroundColor3 = Color3.fromRGB(45,45,60)
    opt.BackgroundTransparency = 0.2
    opt.BorderSizePixel = 0
    opt.Parent = idiomaMenu
    local optCorner = Instance.new("UICorner")
    optCorner.CornerRadius = UDim.new(0,6)
    optCorner.Parent = opt
    opt.MouseButton1Click:Connect(function()
        idiomaAtual = idioma.codigo
        idiomaIndex = i
        idiomaBtn.Text = "🌐 " .. t("idioma") .. ": " .. idioma.nome
        idiomaMenu.Visible = false
        idiomaMenu.Size = UDim2.new(0.9,0,0,0)
        configs.idioma = idiomaAtual
        salvarConfiguracoes()
        print(t("idioma_alterado") .. idioma.nome)
    end)
end

idiomaBtn.MouseButton1Click:Connect(function()
    idiomaMenu.Visible = not idiomaMenu.Visible
    if idiomaMenu.Visible then
        local totalHeight = #listaIdiomas * 32
        local maxHeight = 150
        idiomaMenu.Size = UDim2.new(0.9,0,0,math.min(totalHeight,maxHeight))
        idiomaMenu.CanvasSize = UDim2.new(0,0,0,totalHeight)
    else
        idiomaMenu.Size = UDim2.new(0.9,0,0,0)
    end
end)

addLabel(configCard, t("config_salvas"), Color3.fromRGB(150,150,150))
addLabel(configCard, t("arquivo_config"), Color3.fromRGB(100,200,255))

-- ============================================================
-- FORÇA ATUALIZAÇÃO DO SCROLL
-- ============================================================
task.wait(0.5)
for _, frame in pairs(abaFrames) do
    local flayout = frame:FindFirstChildWhichIsA("UIListLayout")
    if flayout then
        frame.CanvasSize = UDim2.new(0,0,0,flayout.AbsoluteContentSize.Y + 20)
    end
end

print("✅ DAVI HUB CARREGADO COM SUCESSO!")
print("📌 Abas: Main | Menu | Tele | N1 | N2 | N3 | ESP | Geral | Config")
print("💡 Arraste pelo cabeçalho laranja | Use o scroll em cada aba")
print("🔑 Digite /davi ou /hub no chat para ver usuarios")
