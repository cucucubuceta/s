local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local ESPEnabled = false
local ESPObjects = {}

local function createESP(player)
    local highlight = Instance.new("Highlight")
    highlight.Adornee = player.Character
    highlight.FillColor = Color3.new(1, 0, 0) -- Cor do ESP (vermelho)
    highlight.FillTransparency = 0.5 -- Transparência do ESP
    highlight.OutlineColor = Color3.new(1, 1, 1) -- Cor da borda
    highlight.OutlineTransparency = 0 -- Transparência da borda
    highlight.Parent = player.Character
    table.insert(ESPObjects, highlight)
end

local function removeESP(player)
    for i, obj in ipairs(ESPObjects) do
        if obj.Adornee == player.Character then
            obj:Destroy()
            table.remove(ESPObjects, i)
            break
        end
    end
end

local function toggleESP()
    ESPEnabled = not ESPEnabled
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            if ESPEnabled then
                createESP(player)
            else
                removeESP(player)
            end
        end
    end
end

-- Conectar a função de ativação/desativação à tecla "P"
local UserInputService = game:GetService("UserInputService")
UserInputService.InputBegan:Connect(function(input, gameProcessedEvent)
    if not gameProcessedEvent and input.KeyCode == Enum.KeyCode.P then
        toggleESP()
    end
end)

-- Adicionar ESP para jogadores que já estão no jogo
for _, player in ipairs(Players:GetPlayers()) do
    if player ~= LocalPlayer then
        createESP(player)
    end
end

-- Adicionar ESP para novos jogadores que entrarem
Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function()
        if ESPEnabled then
            createESP(player)
        end
    end)
end)

-- Remover ESP quando um jogador sair
Players.PlayerRemoving:Connect(function(player)
    removeESP(player)
end)
