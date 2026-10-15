local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Debris = game:GetService("Debris")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")
local backpack = player:WaitForChild("Backpack")

local normalSpeed = 16
local glitchSpeed = 70
local glitchEnabled = false

-- GUI
local gui = Instance.new("ScreenGui")
gui.Name = "PrankHub"
gui.ResetOnSpawn = false
gui.Parent = playerGui

local frame = Instance.new("Frame")
frame.Size = UDim2.fromOffset(280, 270)
frame.Position = UDim2.new(0, 25, 0.5, -135)
frame.BackgroundColor3 = Color3.fromRGB(22, 22, 30)
frame.BorderSizePixel = 0
frame.Parent = gui

Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 14)

local stroke = Instance.new("UIStroke")
stroke.Color = Color3.fromRGB(145, 75, 255)
stroke.Thickness = 2
stroke.Parent = frame

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, -48, 0, 42)
title.Position = UDim2.fromOffset(12, 5)
title.BackgroundTransparency = 1
title.Text = "⚡ PRANK HUB"
title.TextColor3 = Color3.fromRGB(190, 130, 255)
title.Font = Enum.Font.GothamBold
title.TextSize = 20
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = frame

local close = Instance.new("TextButton")
close.Size = UDim2.fromOffset(30, 30)
close.Position = UDim2.new(1, -38, 0, 9)
close.Text = "X"
close.TextColor3 = Color3.new(1, 1, 1)
close.BackgroundColor3 = Color3.fromRGB(55, 55, 65)
close.Font = Enum.Font.GothamBold
close.TextSize = 14
close.Parent = frame
Instance.new("UICorner", close).CornerRadius = UDim.new(0, 8)

local function makeLabel(text, y, color)
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -24, 0, 25)
    label.Position = UDim2.fromOffset(12, y)
    label.BackgroundTransparency = 1
    label.Text = text
    label.TextColor3 = color
    label.Font = Enum.Font.GothamBold
    label.TextSize = 14
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = frame
    return label
end

local function makeButton(text, y, color)
    local button = Instance.new("TextButton")
    button.Size = UDim2.new(1, -24, 0, 40)
    button.Position = UDim2.fromOffset(12, y)
    button.BackgroundColor3 = color
    button.Text = text
    button.TextColor3 = Color3.new(1, 1, 1)
    button.Font = Enum.Font.GothamBold
    button.TextSize = 14
    button.Parent = frame
    Instance.new("UICorner", button).CornerRadius = UDim.new(0, 8)
    return button
end

makeLabel("💣 BOMB PRANK", 48, Color3.fromRGB(255, 135, 65))

local bombInfo = makeLabel(
    "Tool será adicionada ao inventário",
    76,
    Color3.fromRGB(190, 190, 200)
)
bombInfo.Font = Enum.Font.Gotham
bombInfo.TextSize = 11

makeLabel("⚡ GLITCH SPEED", 119, Color3.fromRGB(190, 130, 255))

local speedButton = makeButton(
    "GLITCH: OFF",
    149,
    Color3.fromRGB(65, 55, 85)
)

local speedInfo = makeLabel("Velocidade: 70 com Shift Lock", 195,
    Color3.fromRGB(185, 185, 195))
speedInfo.Font = Enum.Font.Gotham
speedInfo.TextSize = 11

-- Cria uma Tool verdadeira
local function createBombTool()
    local existing = backpack:FindFirstChild("BombPrank")
    local character = player.Character

    if not existing and character then
        existing = character:FindFirstChild("BombPrank")
    end

    if existing then
        bombInfo.Text = "Tool já está no inventário!"
        return
    end

    local tool = Instance.new("Tool")
    tool.Name = "BombPrank"
    tool.ToolTip = "Bomb Prank"
    tool.RequiresHandle = true
    tool.CanBeDropped = false

    local handle = Instance.new("Part")
    handle.Name = "Handle"
    handle.Shape = Enum.PartType.Ball
    handle.Size = Vector3.new(0.8, 0.8, 0.8)
    handle.Material = Enum.Material.Metal
    handle.Color = Color3.fromRGB(30, 30, 35)
    handle.CanCollide = false
    handle.Massless = true
    handle.Parent = tool

    tool.Activated:Connect(function()
        local char = player.Character
        local root = char and char:FindFirstChild("HumanoidRootPart")
        if not root or tool.Parent ~= char then
            return
        end

        local bomb = Instance.new("Part")
        bomb.Name = "BombPrankBall"
        bomb.Shape = Enum.PartType.Ball
        bomb.Size = Vector3.new(1.2, 1.2, 1.2)
        bomb.Material = Enum.Material.Metal
        bomb.Color = Color3.fromRGB(25, 25, 30)
        bomb.CanCollide = true
        bomb.Anchored = false
        bomb.CFrame = root.CFrame * CFrame.new(0, -2.5, -1.5)
        bomb.Parent = workspace

        local fire = Instance.new("Fire")
        fire.Size = 3
        fire.Heat = 8
        fire.Parent = bomb

        local smoke = Instance.new("Smoke")
        smoke.Color = Color3.fromRGB(55, 55, 55)
        smoke.Size = 2
        smoke.RiseVelocity = 5
        smoke.Parent = bomb

        local sparks = Instance.new("Sparkles")
        sparks.SparkleColor = Color3.fromRGB(255, 130, 20)
        sparks.Parent = bomb

        local light = Instance.new("PointLight")
        light.Color = Color3.fromRGB(255, 90, 15)
        light.Brightness = 3
        light.Range = 8
        light.Parent = bomb

        bomb.AssemblyLinearVelocity =
            -root.CFrame.LookVector * 4 + Vector3.new(0, 2, 0)

        Debris:AddItem(bomb, 2)
    end)

    tool.Parent = backpack
    bombInfo.Text = "Tool criada! Veja o inventário."
end

-- Adiciona a Tool ao iniciar
createBombTool()

player.CharacterAdded:Connect(function()
    task.wait(1)
    backpack = player:WaitForChild("Backpack")
    createBombTool()
end)

-- Glitch Speed
local function updateSpeed()
    local character = player.Character
    local humanoid = character and character:FindFirstChildOfClass("Humanoid")
    if not humanoid then
        return
    end

    local shiftLock =
        UserInputService.MouseBehavior == Enum.MouseBehavior.LockCenter

    if glitchEnabled and shiftLock then
        humanoid.WalkSpeed = glitchSpeed
        speedInfo.Text = "GLITCH ATIVO • SPEED 70"
        speedInfo.TextColor3 = Color3.fromRGB(100, 255, 140)
    else
        humanoid.WalkSpeed = normalSpeed

        if glitchEnabled then
            speedInfo.Text = "Ative o Shift Lock"
            speedInfo.TextColor3 = Color3.fromRGB(255, 190, 80)
        else
            speedInfo.Text = "Velocidade: 70 com Shift Lock"
            speedInfo.TextColor3 = Color3.fromRGB(185, 185, 195)
        end
    end
end

speedButton.Activated:Connect(function()
    glitchEnabled = not glitchEnabled

    if glitchEnabled then
        speedButton.Text = "GLITCH: ON"
        speedButton.BackgroundColor3 = Color3.fromRGB(120, 65, 220)
    else
        speedButton.Text = "GLITCH: OFF"
        speedButton.BackgroundColor3 = Color3.fromRGB(65, 55, 85)
    end

    updateSpeed()
end)

RunService.RenderStepped:Connect(function()
    if glitchEnabled then
        updateSpeed()
    end
end)

close.Activated:Connect(function()
    glitchEnabled = false

    local character = player.Character
    local humanoid = character and character:FindFirstChildOfClass("Humanoid")

    if humanoid then
        humanoid.WalkSpeed = normalSpeed
    end

    gui:Destroy()
end)
