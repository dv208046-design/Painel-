--=========================================================
-- ⚡ Poderoso Hub ⚡ | Brookhaven + Painel ADM Integrado
-- by Luís22 e hiro
--=========================================================

-- Serviços
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local TextChatService = game:GetService("TextChatService")
local StarterGui = game:GetService("StarterGui")
local Workspace = game:GetService("Workspace")
local player = Players.LocalPlayer or Players.PlayerAdded:Wait()

-- Lista de administradores
local admins = {
    ["grabriel_9990"] = true,
    ["braiozinho2020"] = true,
    ["Bielzin999bd"] = true,
    ["Dev_ksjdjdismznd"] = true, -- 🛠️ Novo admin
    ["Jondoe4756"] = true        -- 🛠️ Novo admin
}

--------------------------------------------------------
-- HUB PRINCIPAL (Carrega pra todos)
--------------------------------------------------------
local redzlib = loadstring(game:HttpGet("https://raw.githubusercontent.com/tbao143/Library-ui/refs/heads/main/Redzhubui"))()

local Window = redzlib:MakeWindow({
    Title = "⚡ Poderoso Hub ⚡ | Brookhaven🏘️ 1.0 Pro",
    SubTitle = "by Luís22 e hiro",
    SaveFolder = "Poderoso Hub"
})

local Tab1  = Window:MakeTab({"Credits", "info"})
local Tab2  = Window:MakeTab({"Fun", "fun"})
local Tab3  = Window:MakeTab({"Avatar", "shirt"})
local Tab4  = Window:MakeTab({"House", "Home"})
local Tab5  = Window:MakeTab({"Car", "Car"})
local Tab6  = Window:MakeTab({"RGB", "brush"})
local Tab7  = Window:MakeTab({"Music All", "radio"})
local Tab8  = Window:MakeTab({"Music", "music"})
local Tab9  = Window:MakeTab({"Troll", "skull"})
local Tab10 = Window:MakeTab({"Lag Server", "bomb"})
local Tab11 = Window:MakeTab({"Painel ADM", "shield"})
local Tab12 = Window:MakeTab({"Teleportes", "map-pin"})
local Tab14 = Window:MakeTab({"Premium", "gamepass"})

-- Notificação geral
StarterGui:SetCore("SendNotification", {
    Title = "⚡ Poderoso Hub ⚡",
    Text = "Carregado com sucesso!",
    Duration = 3
})

--------------------------------------------------------
-- PAINEL ADMIN (Carrega só pra administradores)
--------------------------------------------------------
if not admins[player.Name] then
    Tab11:AddLabel("⚠️ Somente administradores podem acessar o Painel ADM.")
    return
end

-- Carregar WindUI
local WindUI = loadstring(game:HttpGet("https://github.com/Footagesus/WindUI/releases/latest/download/main.lua"))()

local AdminWindow = WindUI:CreateWindow({
    Title = "⚡ poderoso hub ⚡ | administração_2.0",
    Icon = "shield",
    Author = "by Luís22 e hiro",
    Theme = "Dark",
    Size = UDim2.fromOffset(330, 460),
    Transparent = true
})

-- Abas do painel de administração
local MainTab = AdminWindow:Tab({Title="Comandos Gerais", Icon="zap", HideSubtitle=true})
WindUI:Notify({
    Title = "Painel Admin",
    Content = "⚡ Painel ADM ativado - bem vindo "..player.DisplayName,
    Duration = 3,
    Icon = "shield",
})

-- Dropdown de jogadores
local selectedPlayer = nil
local playerNames = {}
local Dropdown

local function updateDropdown()
    playerNames = {}
    for _, plr in ipairs(Players:GetPlayers()) do
        table.insert(playerNames, plr.Name)
    end
    if Dropdown then Dropdown:Remove() end
    Dropdown = MainTab:Dropdown({
        Title="Selecionar Jogador",
        Values=playerNames,
        Multi=false,
        Callback=function(value)
            selectedPlayer = Players:FindFirstChild(value)
            if selectedPlayer then
                WindUI:Notify({Title="✅ Selecionado", Content=selectedPlayer.DisplayName, Duration=2, Icon="user"})
            end
        end
    })
end

Players.PlayerAdded:Connect(updateDropdown)
Players.PlayerRemoving:Connect(updateDropdown)
task.delay(1, updateDropdown)

-- Função que envia o comando no chat
local function sendChatCommand(command, target)
    local msg = ";"..command
    if target then msg = msg.." "..target.Name end
    if TextChatService.ChatVersion == Enum.ChatVersion.TextChatService then
        local channel = TextChatService.TextChannels:FindFirstChild("RBXGeneral")
        if channel then channel:SendAsync(msg) end
    else
        local ev = ReplicatedStorage:FindFirstChild("DefaultChatSystemChatEvents")
        if ev and ev:FindFirstChild("SayMessageRequest") then
            ev.SayMessageRequest:FireServer(msg, "All")
        end
    end
end

-- Função principal de execução de comandos
local function executeCommand(cmd)
    if not selectedPlayer then
        WindUI:Notify({Title="Erro", Content="Nenhum jogador selecionado!", Duration=2, Icon="x-circle"})
        return
    end

    sendChatCommand(cmd, selectedPlayer)

    local char = selectedPlayer.Character
    local humanoid = char and char:FindFirstChild("Humanoid")
    local root = char and char:FindFirstChild("HumanoidRootPart")
    local myRoot = player.Character and player.Character:FindFirstChild("HumanoidRootPart")

    if cmd == "kick" then
        pcall(function() selectedPlayer:Kick("Você foi kickado pelo admin ⚡ poderoso hub ⚡") end)
    elseif cmd == "jail" and root then
        local jail = Instance.new("Model")
        jail.Name = "AdminJail_"..selectedPlayer.Name
        jail.Parent = workspace
        local function createWall(size,pos)
            local wall = Instance.new("Part")
            wall.Size = size
            wall.Anchored = true
            wall.CanCollide = true
            wall.BrickColor = BrickColor.new("Really black")
            wall.Position = root.Position + pos
            wall.Parent = jail
        end
        createWall(Vector3.new(10,1,10),Vector3.new(0,-0.5,0))
        createWall(Vector3.new(10,20,1),Vector3.new(0,10,5))
        createWall(Vector3.new(10,20,1),Vector3.new(0,10,-5))
        createWall(Vector3.new(1,20,10),Vector3.new(5,10,0))
        createWall(Vector3.new(1,20,10),Vector3.new(-5,10,0))
        createWall(Vector3.new(10,1,10),Vector3.new(0,20,0))
        if humanoid then humanoid.WalkSpeed, humanoid.JumpPower = 0,0 end
    elseif cmd == "unjail" then
        local jail = workspace:FindFirstChild("AdminJail_"..selectedPlayer.Name)
        if jail then jail:Destroy() end
        if humanoid then humanoid.WalkSpeed, humanoid.JumpPower = 16,50 end
    elseif cmd == "freeze" and humanoid then
        humanoid.WalkSpeed, humanoid.JumpPower = 0,0
        humanoid.PlatformStand = true
    elseif cmd == "unfreeze" and humanoid then
        humanoid.WalkSpeed, humanoid.JumpPower = 16,50
        humanoid.PlatformStand = false
    elseif cmd == "godmode" and humanoid then
        humanoid.MaxHealth = math.huge
        humanoid.Health = math.huge
    elseif cmd == "tp" and root and myRoot then
        root.CFrame = myRoot.CFrame * CFrame.new(0,0,-3)
    elseif cmd == "bring" and root and myRoot then
        root.CFrame = myRoot.CFrame * CFrame.new(0,0,3)
    elseif cmd == "kill" and humanoid then
        humanoid.Health = 0
    elseif cmd == "speed" and humanoid then
        humanoid.WalkSpeed = 100
    elseif cmd == "verify" then
        WindUI:Notify({Title="✅ VERIFIED ✅", Content=selectedPlayer.DisplayName.." verificado!", Duration=3, Icon="check-circle"})
    end
end

-- Botões de comando
local comandoTabela = {
    {"Kick","kick"},{"Jail","jail"},{"Unjail","unjail"},{"Freeze","freeze"},
    {"Unfreeze","unfreeze"},{"Godmode","godmode"},{"TP","tp"},{"Bring","bring"},
    {"Kill","kill"},{"Speed","speed"},{"Verify","verify"}
}

for _, item in ipairs(comandoTabela) do
    MainTab:Button({
        Title = item[1],
        Desc = "Executar "..item[1],
        Callback = function() executeCommand(item[2]) end
    })
end

-- Comandos via chat (ex: ;kick Jogador)
local function ProcessCommand(message)
    if string.sub(message,1,1) ~= ";" then return end
    local content = string.sub(message,2)
    local split = string.split(content," ")
    local cmd = split[1]:lower()
    local targetName = table.concat(split, " ", 2)
    local target = Players:FindFirstChild(targetName)
    if not target then
        WindUI:Notify({Title="❌ Jogador não encontrado", Content=targetName, Duration=2, Icon="x-circle"})
        return
    end
    selectedPlayer = target
    executeCommand(cmd)
end

if TextChatService.ChatVersion == Enum.ChatVersion.TextChatService then
    local channel = TextChatService.TextChannels:FindFirstChild("RBXGeneral")
    if channel then
        channel.OnMessageDoneFiltering:Connect(function(msg)
            if msg.Text then ProcessCommand(msg.Text) end
        end)
    end
else
    local chatEvent = ReplicatedStorage:FindFirstChild("DefaultChatSystemChatEvents")
    if chatEvent and chatEvent:FindFirstChild("OnMessageDoneFiltering") then
        chatEvent.OnMessageDoneFiltering.OnClientEvent:Connect(function(msg)
            if msg.Message then ProcessCommand(msg.Message) end
        end)
    end
end

WindUI:Notify({Title="⚡ Hub ADM", Content="Painel ADM inicializado com sucesso.", Duration=3, Icon="shield"})# Painel-
