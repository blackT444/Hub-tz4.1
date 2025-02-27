local Lighting = game:GetService("Lighting")

-- Remover todos os efeitos visuais na Lighting
for _, effect in pairs(Lighting:GetChildren()) do
    if effect:IsA("PostEffect") or effect:IsA("Atmosphere") then
        effect:Destroy()
    end
end

-- Resetar configurações da Lighting para um ambiente claro
Lighting.Brightness = 2
Lighting.Ambient = Color3.new(1, 1, 1) -- Deixa tudo claro
Lighting.OutdoorAmbient = Color3.new(1, 1, 1)
Lighting.FogEnd = 100000 -- Remove neblina
Lighting.FogStart = 100000 -- Garante que a neblina não apareça
Lighting.ClockTime = 14 -- Define o horário para a tarde (claro)
Lighting.GlobalShadows = false -- Remove sombras globais

-- Criar tela principal
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "PlayerESP"
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

-- Frame para botão ON/OFF
local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0.15, 0, 0.07, 0)
Frame.Position = UDim2.new(0.4, 0, 0.85, 0)
Frame.BackgroundTransparency = 0.5
Frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Frame.Parent = ScreenGui

local ToggleButton = Instance.new("TextButton")
ToggleButton.Size = UDim2.new(0.45, 0, 1, 0)
ToggleButton.BackgroundTransparency = 1
ToggleButton.TextScaled = true
ToggleButton.Text = "ON"
ToggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleButton.Parent = Frame

local SpeedButton = Instance.new("TextButton")
SpeedButton.Size = UDim2.new(0.45, 0, 1, 0)
SpeedButton.Position = UDim2.new(0.55, 0, 0, 0)
SpeedButton.BackgroundTransparency = 1
SpeedButton.TextScaled = true
SpeedButton.Text = "F"
SpeedButton.TextColor3 = Color3.fromRGB(255, 255, 255)
SpeedButton.Parent = Frame

local showESP = true

-- Função para criar ESP no jogador
local function createESP(player)
    if player.Character and player.Character:FindFirstChild("Head") then
        local highlight = Instance.new("Highlight")
        highlight.Name = "ESP_Highlight"
        highlight.Parent = player.Character
        highlight.FillTransparency = 1
        highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
        highlight.OutlineTransparency = 0
        highlight.Enabled = showESP
    end
end

local function addESPToPlayers()
    for _, player in pairs(game.Players:GetPlayers()) do
        if player ~= game.Players.LocalPlayer then
            createESP(player)
        end
    end
end

game.Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function(character)
        wait(1)
        createESP(player)
    end)
end)

addESPToPlayers()

local function updateESP()
    for _, player in pairs(game.Players:GetPlayers()) do
        if player ~= game.Players.LocalPlayer and player.Character then
            local highlight = player.Character:FindFirstChild("ESP_Highlight")
            if highlight then
                highlight.Enabled = showESP
            end
        end
    end
end

local function toggleESP()
    showESP = not showESP
    updateESP()
    ToggleButton.Text = showESP and "ON" or "OFF"
end

ToggleButton.MouseButton1Click:Connect(toggleESP)

-- Função para REMOVER a luz do avatar
local function removePlayerLight()
    local player = game.Players.LocalPlayer
    if player.Character then
        local head = player.Character:FindFirstChild("Head")
        if head then
            local light = head:FindFirstChild("ESP_Light")
            if light then
                light:Destroy() -- Remove a luz do avatar
            end
        end
    end
end

-- Remove a luz do jogador sempre que o personagem carregar
game.Players.LocalPlayer.CharacterAdded:Connect(function()
    wait(1)
    removePlayerLight()
end)

removePlayerLight() -- Garante que a luz seja removida no início

-- Ajustar velocidade do jogador
local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()

local walkSpeed = 16
local speedEnabled = false

SpeedButton.MouseButton1Click:Connect(function()
    speedEnabled = not speedEnabled
    if speedEnabled then
        walkSpeed = 24
        SpeedButton.Text = "S"
    else
        walkSpeed = 16
        SpeedButton.Text = "F"
    end
    character:WaitForChild("Humanoid").WalkSpeed = walkSpeed
end)

character:WaitForChild("Humanoid").Jumping:Connect(function()
    character:WaitForChild("Humanoid").WalkSpeed = walkSpeed
end)
