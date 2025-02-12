-- Referências importantes
local player = game.Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")
local backpack = player:WaitForChild("Backpack")
local fishingRod = backpack:WaitForChild("FishingRod")  -- Supondo que a vara de pesca esteja no Backpack

-- Criando a ScreenGui para o Hub de Pesca
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = playerGui
screenGui.Name = "FishingHubGUI"
screenGui.ResetOnSpawn = false

-- Criando o Menu Frame
local menuFrame = Instance.new("Frame")
menuFrame.Parent = screenGui
menuFrame.Size = UDim2.new(0, 400, 0, 300)
menuFrame.Position = UDim2.new(0.5, -200, 0.5, -150)
menuFrame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
menuFrame.BorderSizePixel = 0
menuFrame.BackgroundTransparency = 0.5
menuFrame.AnchorPoint = Vector2.new(0.5, 0.5)
menuFrame.Visible = true

-- Criando o botão de Auto Fishing
local autoFishingButton = Instance.new("TextButton")
autoFishingButton.Parent = menuFrame
autoFishingButton.Size = UDim2.new(0, 200, 0, 50)
autoFishingButton.Position = UDim2.new(0.5, -100, 0, 20)
autoFishingButton.BackgroundColor3 = Color3.fromRGB(100, 255, 100)
autoFishingButton.Text = "Enable Auto Fishing"
autoFishingButton.TextColor3 = Color3.fromRGB(0, 0, 0)
autoFishingButton.TextSize = 20

-- Criando o botão de Auto Shake
local autoShakeButton = Instance.new("TextButton")
autoShakeButton.Parent = menuFrame
autoShakeButton.Size = UDim2.new(0, 200, 0, 50)
autoShakeButton.Position = UDim2.new(0.5, -100, 0, 80)
autoShakeButton.BackgroundColor3 = Color3.fromRGB(255, 100, 100)
autoShakeButton.Text = "Enable Auto Shake"
autoShakeButton.TextColor3 = Color3.fromRGB(0, 0, 0)
autoShakeButton.TextSize = 20

-- Criando o botão de Auto Fish (Pescar imediatamente)
local autoFishButton = Instance.new("TextButton")
autoFishButton.Parent = menuFrame
autoFishButton.Size = UDim2.new(0, 200, 0, 50)
autoFishButton.Position = UDim2.new(0.5, -100, 0, 140)
autoFishButton.BackgroundColor3 = Color3.fromRGB(100, 100, 255)
autoFishButton.Text = "Enable Auto Fish"
autoFishButton.TextColor3 = Color3.fromRGB(0, 0, 0)
autoFishButton.TextSize = 20

-- Criando o botão de ajuste de velocidade
local speedButton = Instance.new("TextButton")
speedButton.Parent = menuFrame
speedButton.Size = UDim2.new(0, 200, 0, 50)
speedButton.Position = UDim2.new(0.5, -100, 0, 200)
speedButton.BackgroundColor3 = Color3.fromRGB(255, 255, 100)
speedButton.Text = "Speed: Normal"
speedButton.TextColor3 = Color3.fromRGB(0, 0, 0)
speedButton.TextSize = 20

-- Variáveis de controle
local isAutoFishing = false
local isAutoShaking = false
local isFishing = false
local fishingSpeed = 1  -- Velocidade de pesca (1 = normal)

-- Função para alternar a visibilidade do menu
local function toggleMenu()
    menuFrame.Visible = not menuFrame.Visible
end

-- Função para simular pesca
local function startFishing()
    if isFishing then return end
    isFishing = true
    -- Simulando pesca criando e destruindo um peixe
    wait(math.random(2, 5) * fishingSpeed)  -- Tempo aleatório multiplicado pela velocidade
    local fish = Instance.new("Part")
    fish.Size = Vector3.new(3, 1, 1)
    fish.Position = workspace.FishingArea.Position + Vector3.new(math.random(-10, 10), 0, math.random(-10, 10))
    fish.Anchored = true
    fish.BrickColor = BrickColor.new("Bright blue")
    fish.Parent = workspace
    wait(3)
    fish:Destroy()
    isFishing = false
end

-- Função para ativar o Auto Fishing
local function activateAutoFishing()
    isAutoFishing = true
    autoFishingButton.Text = "Disable Auto Fishing"  -- Alterando o texto do botão
    while isAutoFishing do
        startFishing()
        wait(math.random(5, 10) * fishingSpeed)  -- Intervalo aleatório entre pescas, ajustado pela velocidade
    end
end

-- Função para desativar o Auto Fishing
local function deactivateAutoFishing()
    isAutoFishing = false
    autoFishingButton.Text = "Enable Auto Fishing"  -- Alterando o texto do botão
end

-- Função para simular Auto Shake da vara de pesca
local function startAutoShake()
    isAutoShaking = true
    autoShakeButton.Text = "Disable Auto Shake"  -- Alterando o texto do botão
    while isAutoShaking do
        fishingRod.CFrame = fishingRod.CFrame * CFrame.Angles(0, math.rad(math.random(-5, 5)), 0)
        wait(0.1)  -- Intervalo entre os movimentos
    end
end

-- Função para parar o Auto Shake
local function stopAutoShake()
    isAutoShaking = false
    autoShakeButton.Text = "Enable Auto Shake"  -- Alterando o texto do botão
end

-- Função para ativar o Auto Fish (pescar imediatamente)
local function activateAutoFish()
    startFishing()  -- Executando uma pesca imediata
end

-- Função para alternar o menu com a tecla Ctrl
local userInputService = game:GetService("UserInputService")
userInputService.InputBegan:Connect(function(input, gameProcessedEvent)
    if not gameProcessedEvent and input.UserInputType == Enum.UserInputType.Keyboard then
        if input.KeyCode == Enum.KeyCode.LeftControl or input.KeyCode == Enum.KeyCode.RightControl then
            toggleMenu()  -- Alternando a visibilidade do menu
        end
    end
end)

-- Configurando os botões com as funcionalidades
autoFishingButton.MouseButton1Click:Connect(function()
    if isAutoFishing then
        deactivateAutoFishing()  -- Desativando o Auto Fishing
    else
        activateAutoFishing()  -- Ativando o Auto Fishing
    end
end)

autoShakeButton.MouseButton1Click:Connect(function()
    if isAutoShaking then
        stopAutoShake()  -- Parando o Auto Shake
    else
        startAutoShake()  -- Iniciando o Auto Shake
    end
end)

autoFishButton.MouseButton1Click:Connect(function()
    activateAutoFish()  -- Executando uma pesca imediata
end)

-- Função para alterar a velocidade de pesca
local function changeFishingSpeed()
    if fishingSpeed == 1 then
        fishingSpeed = 2  -- Aumentando a velocidade
        speedButton.Text = "Speed: Fast"
    elseif fishingSpeed == 2 then
        fishingSpeed = 3  -- Aumentando mais a velocidade
        speedButton.Text = "Speed: Faster"
    else
        fishingSpeed = 1  -- Resetando a velocidade para normal
        speedButton.Text = "Speed: Normal"
    end
end

-- Conectando o botão de velocidade para mudar a velocidade de pesca
speedButton.MouseButton1Click:Connect(changeFishingSpeed)
