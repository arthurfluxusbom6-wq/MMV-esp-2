-- Serviços essenciais
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

-- Configurações do ESP
local ESP_CONFIG = {
    Assassino = {Cor = Color3.new(1, 0, 0), Espessura = 2}, -- Vermelho
    Xerife = {Cor = Color3.new(0, 0, 1), Espessura = 2} -- Azul
}

-- Função para criar o ESP de um jogador
local function criarESP(jogador)
    local personagem = jogador.Character
    if not personagem or not personagem:FindFirstChild("HumanoidRootPart") then return end

    -- Cria o BillboardGui
    local billboard = Instance.new("BillboardGui")
    billboard.Name = "PlayerESP"
    billboard.Adornee = personagem.HumanoidRootPart
    billboard.Size = UDim2.new(0, 200, 0, 50)
    billboard.StudsOffset = Vector3.new(0, 3, 0)
    billboard.Parent = personagem.HumanoidRootPart

    -- Texto com nome do jogador e papel
    local texto = Instance.new("TextLabel")
    texto.Name = "RoleText"
    texto.Size = UDim2.new(1, 0, 1, 0)
    texto.BackgroundTransparency = 1
    texto.TextScaled = true
    texto.Font = Enum.Font.SourceSansBold
    texto.Parent = billboard

    -- Caixa ao redor do personagem
    local caixa = Instance.new("BoxHandleAdornment")
    caixa.Name = "PlayerBox"
    caixa.Adornee = personagem.HumanoidRootPart
    caixa.Size = personagem:GetExtentsSize() + Vector3.new(0.2, 0.2, 0.2)
    caixa.Transparency = 0.7
    caixa.ZIndex = 5
    caixa.Parent = personagem.HumanoidRootPart

    -- Atualiza o ESP conforme o papel do jogador
    local function atualizarPapel()
        local papel = jogador:GetAttribute("Role") -- A maioria dos scripts usa esse atributo para armazenar o papel
        
        if papel == "Assassin" then
            texto.Text = "[ASSASSINO] " .. jogador.Name
            texto.TextColor3 = ESP_CONFIG.Assassino.Cor
            caixa.Color3 = ESP_CONFIG.Assassino.Cor
            caixa.Thickness = ESP_CONFIG.Assassino.Espessura
        elseif papel == "Sheriff" then
            texto.Text = "[XERIFE] " .. jogador.Name
            texto.TextColor3 = ESP_CONFIG.Xerife.Cor
            caixa.Color3 = ESP_CONFIG.Xerife.Cor
            caixa.Thickness = ESP_CONFIG.Xerife.Espessura
        else
            -- Remove o ESP se for um inocente
            billboard:Destroy()
            caixa:Destroy()
        end
    end

    -- Atualiza quando o papel mudar ou o personagem for atualizado
    jogador:GetAttributeChangedSignal("Role"):Connect(atualizarPapel)
    personagem.ChildAdded:Connect(function(child)
        if child.Name == "HumanoidRootPart" then
            atualizarPapel()
        end
    end)

    atualizarPapel()
end

-- Inicializa o ESP para jogadores existentes e novos
for _, jogador in ipairs(Players:GetPlayers()) do
    criarESP(jogador)
end

Players.PlayerAdded:Connect(function(jogador)
    jogador.CharacterAdded:Connect(function()
        criarESP(jogador)
    end)
end)
