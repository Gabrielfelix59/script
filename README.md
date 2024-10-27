local player = game.Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

local ScreenGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local TitleLabel = Instance.new("TextLabel")
local ToggleFarmButton = Instance.new("TextButton")
local StatusLabel = Instance.new("TextLabel")
local LevelLabel = Instance.new("TextLabel")
local RefreshButton = Instance.new("TextButton")
local IslandSelectionFrame = Instance.new("Frame")
local IslandDropdown = Instance.new("TextButton")
local TeleportButton = Instance.new("TextButton")
local EnemyLabel = Instance.new("TextLabel")
local EnemyCountLabel = Instance.new("TextLabel")

ScreenGui.Parent = playerGui
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
MainFrame.Position = UDim2.new(0.1, 0, 0.1, 0)
MainFrame.Size = UDim2.new(0, 300, 0, 400)

TitleLabel.Parent = MainFrame
TitleLabel.BackgroundTransparency = 1
TitleLabel.Size = UDim2.new(0, 300, 0, 30)
TitleLabel.Position = UDim2.new(0, 0, 0, 10)
TitleLabel.Text = "Painel de Controle"
TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleLabel.TextSize = 20
TitleLabel.Font = Enum.Font.SourceSansBold

ToggleFarmButton.Parent = MainFrame
ToggleFarmButton.BackgroundColor3 = Color3.fromRGB(70, 130, 180)
ToggleFarmButton.Size = UDim2.new(0, 280, 0, 30)
ToggleFarmButton.Position = UDim2.new(0, 10, 0, 50)
ToggleFarmButton.Text = "Iniciar Farm"
ToggleFarmButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleFarmButton.TextSize = 16
ToggleFarmButton.Font = Enum.Font.SourceSansBold

StatusLabel.Parent = MainFrame
StatusLabel.BackgroundTransparency = 1
StatusLabel.Size = UDim2.new(0, 280, 0, 20)
StatusLabel.Position = UDim2.new(0, 10, 0, 90)
StatusLabel.Text = "Status: Inativo"
StatusLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
StatusLabel.TextSize = 14

LevelLabel.Parent = MainFrame
LevelLabel.BackgroundTransparency = 1
LevelLabel.Size = UDim2.new(0, 280, 0, 20)
LevelLabel.Position = UDim2.new(0, 10, 0, 110)
LevelLabel.Text = "Nível Atual: "
LevelLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
LevelLabel.TextSize = 14

RefreshButton.Parent = MainFrame
RefreshButton.BackgroundColor3 = Color3.fromRGB(70, 130, 180)
RefreshButton.Size = UDim2.new(0, 280, 0, 30)
RefreshButton.Position = UDim2.new(0, 10, 0, 140)
RefreshButton.Text = "Atualizar Nível"
RefreshButton.TextColor3 = Color3.fromRGB(255, 255, 255)
RefreshButton.TextSize = 16
RefreshButton.Font = Enum.Font.SourceSans

local islands = {
    {name = "Ilha Inicial", position = Vector3.new(100, 50, 200)},
    {name = "Ilha das Macacos", position = Vector3.new(300, 50, 400)},
    {name = "Ilha do Gorila", position = Vector3.new(500, 50, 600)},
}

IslandSelectionFrame.Parent = MainFrame
IslandSelectionFrame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
IslandSelectionFrame.Position = UDim2.new(0, 10, 0, 180)
IslandSelectionFrame.Size = UDim2.new(0, 280, 0, 150)

IslandDropdown.Parent = IslandSelectionFrame
IslandDropdown.Size = UDim2.new(0, 280, 0, 30)
IslandDropdown.Position = UDim2.new(0, 0, 0, 0)
IslandDropdown.Text = "Selecione uma Ilha"
IslandDropdown.TextColor3 = Color3.fromRGB(255, 255, 255)
IslandDropdown.BackgroundColor3 = Color3.fromRGB(70, 130, 180)

TeleportButton.Parent = IslandSelectionFrame
TeleportButton.Size = UDim2.new(0, 280, 0, 30)
TeleportButton.Position = UDim2.new(0, 0, 0, 40)
TeleportButton.Text = "Teletransportar"
TeleportButton.TextColor3 = Color3.fromRGB(255, 255, 255)
TeleportButton.BackgroundColor3 = Color3.fromRGB(70, 130, 180)

local selectedIsland = nil
local farming = false
local enemyCount = 0

local function moveTo(position)
    if player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        player.Character.HumanoidRootPart.CFrame = CFrame.new(position)
    end
end

local function attackEnemy(enemy)
    if enemy and enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 then
        local tool = player.Backpack:FindFirstChildOfClass("Tool")
        if tool then
            player.Character.HumanoidRootPart.CFrame = enemy.HumanoidRootPart.CFrame * CFrame.new(0, 0, -5)
            wait(0.5)
            tool:Activate()
            enemyCount = enemyCount + 1
            EnemyCountLabel.Text = "Inimigos Derrotados: " .. enemyCount
        end
    end
end

local function farmToMaxLevel()
    while farming do
        if player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            for _, enemy in pairs(workspace:GetChildren()) do
                if enemy:IsA("Model") and enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 then
                    attackEnemy(enemy)
                    wait(1)
                end
            end
            wait(2)
        end
    end
end

ToggleFarmButton.MouseButton1Click:Connect(function()
    farming = not farming
    if farming then
        StatusLabel.Text = "Status: Ativo"
        ToggleFarmButton.Text = "Parar Farm"
        enemyCount = 0
        farmToMaxLevel()
    else
        StatusLabel.Text = "Status: Inativo"
        ToggleFarmButton.Text = "Iniciar Farm"
    end
end)

RefreshButton.MouseButton1Click:Connect(function()
    local playerLevel = player:WaitForChild("Data"):WaitForChild("Level").Value
    LevelLabel.Text = "Nível Atual: " .. playerLevel
end)

IslandDropdown.MouseButton1Click:Connect(function()
    selectedIsland = nil
    local islandNames = "Ilhas disponíveis:\n"
    for _, island in ipairs(islands) do
        islandNames = islandNames .. island.name .. "\n"
    end
    IslandDropdown.Text = islandNames
end)

TeleportButton.MouseButton1Click:Connect(function()
    if selectedIsland then
        moveTo(selectedIsland.position)
    else
        warn("Selecione uma ilha primeiro.")
    end
end)

for _, island in ipairs(islands) do
    local button = Instance.new("TextButton")
    button.Size = UDim2.new(0, 280, 0, 30)
    button.Position = UDim2.new(0, 0, 0, 70 + 30 * _) -- Ajuste de posição
    button.Text = island.name
    button.BackgroundColor3 = Color3.fromRGB(100, 149, 237)
    button.TextColor3 = Color3.fromRGB(255, 255, 255)
    button.Parent = IslandSelectionFrame

    button.MouseButton1Click:Connect(function()
        selectedIsland = island
        IslandDropdown.Text = "Selecionado: " .. island.name
    end)
end
