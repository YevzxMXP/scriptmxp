local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local VirtualInputManager = game:GetService("VirtualInputManager")
local Camera = workspace.CurrentCamera
local TweenService = game:GetService("TweenService")
local HttpService = game:GetService("HttpService")
local CoreGui = game:GetService("CoreGui")

local Settings = {
    Theme = "Dark",
    AutoSave = true,
    Notifications = true
}

local Utilities = {
    Aimlock = {
        Enabled = false,
        Target = nil,
        Smoothing = 0.2,
        Sensitivity = 1.0,
        MaxDistance = 60,
        Key = Enum.KeyCode.F,
        Prediction = 0.1,
        BodyPart = "Head",
        UseCustomOffset = false,
        CustomOffset = Vector3.new(0, 0, 0)
    },
    Noclip = {
        Enabled = false,
        Key = Enum.KeyCode.G
    },
    ESP = {
        Enabled = false,
        Key = Enum.KeyCode.H,
        Boxes = true,
        Names = true,
        Distance = true,
        Tracers = false
    }
}

local ESPObjects = {}
local NoclipConnection = nil

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "YevHub_" .. HttpService:GenerateGUID(false):sub(1,8)
ScreenGui.Parent = CoreGui
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.ResetOnSpawn = false

local MainFrame = Instance.new("Frame")
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 30)
MainFrame.BorderSizePixel = 0
MainFrame.Position = UDim2.new(0.02, 0, 0.02, 0)
MainFrame.Size = UDim2.new(0, 320, 0, 450)
MainFrame.Active = true
MainFrame.Draggable = true

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 8)
UICorner.Parent = MainFrame

local Header = Instance.new("Frame")
Header.Parent = MainFrame
Header.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
Header.BorderSizePixel = 0
Header.Size = UDim2.new(1, 0, 0, 40)

local HeaderCorner = Instance.new("UICorner")
HeaderCorner.CornerRadius = UDim.new(0, 8)
HeaderCorner.Parent = Header

local Title = Instance.new("TextLabel")
Title.Parent = Header
Title.BackgroundTransparency = 1
Title.Position = UDim2.new(0, 15, 0, 0)
Title.Size = UDim2.new(1, -30, 1, 0)
Title.Font = Enum.Font.GothamBold
Title.Text = "YEV HUB v2.2"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 16
Title.TextXAlignment = Enum.TextXAlignment.Left

local StatusIndicator = Instance.new("Frame")
StatusIndicator.Parent = Header
StatusIndicator.BackgroundColor3 = Color3.fromRGB(50, 255, 50)
StatusIndicator.BorderSizePixel = 0
StatusIndicator.Position = UDim2.new(1, -20, 0.5, -5)
StatusIndicator.Size = UDim2.new(0, 10, 0, 10)

local StatusCorner = Instance.new("UICorner")
StatusCorner.CornerRadius = UDim.new(1, 0)
StatusCorner.Parent = StatusIndicator

local Content = Instance.new("Frame")
Content.Parent = MainFrame
Content.BackgroundTransparency = 1
Content.Position = UDim2.new(0, 0, 0, 45)
Content.Size = UDim2.new(1, 0, 1, -45)

local StatusPanel = Instance.new("Frame")
StatusPanel.Parent = Content
StatusPanel.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
StatusPanel.BorderSizePixel = 0
StatusPanel.Position = UDim2.new(0, 10, 0, 10)
StatusPanel.Size = UDim2.new(1, -20, 0, 80)

local StatusCorner2 = Instance.new("UICorner")
StatusCorner2.CornerRadius = UDim.new(0, 6)
StatusCorner2.Parent = StatusPanel

local AimlockStatus = Instance.new("TextLabel")
AimlockStatus.Parent = StatusPanel
AimlockStatus.BackgroundTransparency = 1
AimlockStatus.Position = UDim2.new(0, 10, 0, 10)
AimlockStatus.Size = UDim2.new(0.5, -15, 0, 20)
AimlockStatus.Font = Enum.Font.Gotham
AimlockStatus.Text = "AIMLOCK: OFF"
AimlockStatus.TextColor3 = Color3.fromRGB(255, 50, 50)
AimlockStatus.TextSize = 12
AimlockStatus.TextXAlignment = Enum.TextXAlignment.Left

local TargetStatus = Instance.new("TextLabel")
TargetStatus.Parent = StatusPanel
TargetStatus.BackgroundTransparency = 1
TargetStatus.Position = UDim2.new(0, 10, 0, 35)
TargetStatus.Size = UDim2.new(1, -20, 0, 20)
TargetStatus.Font = Enum.Font.Gotham
TargetStatus.Text = "TARGET: NONE"
TargetStatus.TextColor3 = Color3.fromRGB(200, 200, 200)
TargetStatus.TextSize = 11
TargetStatus.TextXAlignment = Enum.TextXAlignment.Left

local ESPStatus = Instance.new("TextLabel")
ESPStatus.Parent = StatusPanel
ESPStatus.BackgroundTransparency = 1
ESPStatus.Position = UDim2.new(0.5, 5, 0, 10)
ESPStatus.Size = UDim2.new(0.5, -15, 0, 20)
ESPStatus.Font = Enum.Font.Gotham
ESPStatus.Text = "ESP: OFF"
ESPStatus.TextColor3 = Color3.fromRGB(255, 50, 50)
ESPStatus.TextSize = 12
ESPStatus.TextXAlignment = Enum.TextXAlignment.Left

local NoclipStatus = Instance.new("TextLabel")
NoclipStatus.Parent = StatusPanel
NoclipStatus.BackgroundTransparency = 1
NoclipStatus.Position = UDim2.new(0.5, 5, 0, 35)
NoclipStatus.Size = UDim2.new(0.5, -15, 0, 20)
NoclipStatus.Font = Enum.Font.Gotham
NoclipStatus.Text = "NOCLIP: OFF"
NoclipStatus.TextColor3 = Color3.fromRGB(255, 50, 50)
NoclipStatus.TextSize = 12
NoclipStatus.TextXAlignment = Enum.TextXAlignment.Left

local ControlsPanel = Instance.new("Frame")
ControlsPanel.Parent = Content
ControlsPanel.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
ControlsPanel.BorderSizePixel = 0
ControlsPanel.Position = UDim2.new(0, 10, 0, 100)
ControlsPanel.Size = UDim2.new(1, -20, 0, 300)

local ControlsCorner = Instance.new("UICorner")
ControlsCorner.CornerRadius = UDim.new(0, 6)
ControlsCorner.Parent = ControlsPanel

local function CreateToggle(name, position, utility, callback)
    local ToggleFrame = Instance.new("Frame")
    ToggleFrame.Parent = ControlsPanel
    ToggleFrame.BackgroundTransparency = 1
    ToggleFrame.Position = position
    ToggleFrame.Size = UDim2.new(1, -20, 0, 30)
    
    local ToggleLabel = Instance.new("TextLabel")
    ToggleLabel.Parent = ToggleFrame
    ToggleLabel.BackgroundTransparency = 1
    ToggleLabel.Position = UDim2.new(0, 0, 0, 0)
    ToggleLabel.Size = UDim2.new(0.6, 0, 1, 0)
    ToggleLabel.Font = Enum.Font.Gotham
    ToggleLabel.Text = name
    ToggleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    ToggleLabel.TextSize = 12
    ToggleLabel.TextXAlignment = Enum.TextXAlignment.Left
    
    local ToggleButton = Instance.new("TextButton")
    ToggleButton.Parent = ToggleFrame
    ToggleButton.BackgroundColor3 = Color3.fromRGB(60, 60, 70)
    ToggleButton.BorderSizePixel = 0
    ToggleButton.Position = UDim2.new(0.6, 0, 0.1, 0)
    ToggleButton.Size = UDim2.new(0.4, 0, 0.8, 0)
    ToggleButton.Font = Enum.Font.Gotham
    ToggleButton.Text = "OFF"
    ToggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    ToggleButton.TextSize = 11
    
    local ToggleCorner = Instance.new("UICorner")
    ToggleCorner.CornerRadius = UDim.new(0, 4)
    ToggleCorner.Parent = ToggleButton
    
    ToggleButton.MouseButton1Click:Connect(function()
        Utilities[utility].Enabled = not Utilities[utility].Enabled
        ToggleButton.Text = Utilities[utility].Enabled and "ON" or "OFF"
        ToggleButton.BackgroundColor3 = Utilities[utility].Enabled and Color3.fromRGB(50, 200, 50) or Color3.fromRGB(60, 60, 70)
        
        if callback then
            callback(Utilities[utility].Enabled)
        end
    end)
    
    return ToggleFrame
end

local function CreateSlider(label, position, minValue, maxValue, currentValue, callback)
    local SliderFrame = Instance.new("Frame")
    SliderFrame.Parent = ControlsPanel
    SliderFrame.BackgroundTransparency = 1
    SliderFrame.Position = position
    SliderFrame.Size = UDim2.new(1, -20, 0, 40)
    
    local SliderLabel = Instance.new("TextLabel")
    SliderLabel.Parent = SliderFrame
    SliderLabel.BackgroundTransparency = 1
    SliderLabel.Position = UDim2.new(0, 0, 0, 0)
    SliderLabel.Size = UDim2.new(1, 0, 0, 15)
    SliderLabel.Font = Enum.Font.Gotham
    SliderLabel.Text = label .. ": " .. currentValue
    SliderLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    SliderLabel.TextSize = 11
    SliderLabel.TextXAlignment = Enum.TextXAlignment.Left
    
    local SliderBackground = Instance.new("Frame")
    SliderBackground.Parent = SliderFrame
    SliderBackground.BackgroundColor3 = Color3.fromRGB(60, 60, 70)
    SliderBackground.BorderSizePixel = 0
    SliderBackground.Position = UDim2.new(0, 0, 0, 20)
    SliderBackground.Size = UDim2.new(1, 0, 0, 5)
    
    local SliderCorner = Instance.new("UICorner")
    SliderCorner.CornerRadius = UDim.new(0, 3)
    SliderCorner.Parent = SliderBackground
    
    local normalizedValue = (currentValue - minValue) / (maxValue - minValue)
    
    local SliderFill = Instance.new("Frame")
    SliderFill.Parent = SliderBackground
    SliderFill.BackgroundColor3 = Color3.fromRGB(50, 150, 255)
    SliderFill.BorderSizePixel = 0
    SliderFill.Size = UDim2.new(normalizedValue, 0, 1, 0)
    
    local SliderFillCorner = Instance.new("UICorner")
    SliderFillCorner.CornerRadius = UDim.new(0, 3)
    SliderFillCorner.Parent = SliderFill
    
    local SliderButton = Instance.new("TextButton")
    SliderButton.Parent = SliderBackground
    SliderButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    SliderButton.BorderSizePixel = 0
    SliderButton.Position = UDim2.new(normalizedValue, -5, -2.5, 0)
    SliderButton.Size = UDim2.new(0, 10, 0, 10)
    SliderButton.Text = ""
    
    local SliderButtonCorner = Instance.new("UICorner")
    SliderButtonCorner.CornerRadius = UDim.new(1, 0)
    SliderButtonCorner.Parent = SliderButton
    
    local dragging = false
    
    local function updateValue(value)
        value = math.clamp(value, minValue, maxValue)
        local normalized = (value - minValue) / (maxValue - minValue)
        SliderLabel.Text = label .. ": " .. string.format("%.1f", value)
        SliderFill.Size = UDim2.new(normalized, 0, 1, 0)
        SliderButton.Position = UDim2.new(normalized, -5, -2.5, 0)
        
        if callback then
            callback(value)
        end
    end
    
    SliderButton.MouseButton1Down:Connect(function()
        dragging = true
    end)
    
    UIS.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)
    
    UIS.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local x = math.clamp((input.Position.X - SliderBackground.AbsolutePosition.X) / SliderBackground.AbsoluteSize.X, 0, 1)
            local value = minValue + (x * (maxValue - minValue))
            updateValue(value)
        end
    end)
    
    return SliderFrame
end

local function CreateBodyPartDropdown()
    local DropdownFrame = Instance.new("Frame")
    DropdownFrame.Parent = ControlsPanel
    DropdownFrame.BackgroundTransparency = 1
    DropdownFrame.Position = UDim2.new(0, 10, 0, 250)
    DropdownFrame.Size = UDim2.new(1, -20, 0, 40)
    
    local DropdownLabel = Instance.new("TextLabel")
    DropdownLabel.Parent = DropdownFrame
    DropdownLabel.BackgroundTransparency = 1
    DropdownLabel.Position = UDim2.new(0, 0, 0, 0)
    DropdownLabel.Size = UDim2.new(0.5, 0, 0, 15)
    DropdownLabel.Font = Enum.Font.Gotham
    DropdownLabel.Text = "MIRAR EM:"
    DropdownLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    DropdownLabel.TextSize = 11
    DropdownLabel.TextXAlignment = Enum.TextXAlignment.Left
    
    local DropdownButton = Instance.new("TextButton")
    DropdownButton.Parent = DropdownFrame
    DropdownButton.BackgroundColor3 = Color3.fromRGB(60, 60, 70)
    DropdownButton.BorderSizePixel = 0
    DropdownButton.Position = UDim2.new(0.5, 0, 0, 0)
    DropdownButton.Size = UDim2.new(0.5, 0, 0, 20)
    DropdownButton.Font = Enum.Font.Gotham
    DropdownButton.Text = Utilities.Aimlock.BodyPart
    DropdownButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    DropdownButton.TextSize = 11
    
    local DropdownCorner = Instance.new("UICorner")
    DropdownCorner.CornerRadius = UDim.new(0, 4)
    DropdownCorner.Parent = DropdownButton
    
    local DropdownList = Instance.new("Frame")
    DropdownList.Parent = DropdownFrame
    DropdownList.BackgroundColor3 = Color3.fromRGB(45, 45, 55)
    DropdownList.BorderSizePixel = 0
    DropdownList.Position = UDim2.new(0.5, 0, 0, 25)
    DropdownList.Size = UDim2.new(0.5, 0, 0, 0)
    DropdownList.ClipsDescendants = true
    DropdownList.Visible = false
    
    local DropdownListCorner = Instance.new("UICorner")
    DropdownListCorner.CornerRadius = UDim.new(0, 4)
    DropdownListCorner.Parent = DropdownList
    
    local bodyParts = {"Head", "Torso", "HumanoidRootPart", "None"}
    
    local function updateBodyPart(part)
        Utilities.Aimlock.BodyPart = part
        DropdownButton.Text = part
        DropdownList.Visible = false
    end
    
    for i, part in ipairs(bodyParts) do
        local PartButton = Instance.new("TextButton")
        PartButton.Parent = DropdownList
        PartButton.BackgroundColor3 = Color3.fromRGB(60, 60, 70)
        PartButton.BorderSizePixel = 0
        PartButton.Position = UDim2.new(0, 0, 0, (i-1)*20)
        PartButton.Size = UDim2.new(1, 0, 0, 20)
        PartButton.Font = Enum.Font.Gotham
        PartButton.Text = part
        PartButton.TextColor3 = Color3.fromRGB(255, 255, 255)
        PartButton.TextSize = 10
        
        PartButton.MouseButton1Click:Connect(function()
            updateBodyPart(part)
        end)
    end
    
    DropdownList.Size = UDim2.new(1, 0, 0, #bodyParts * 20)
    
    DropdownButton.MouseButton1Click:Connect(function()
        DropdownList.Visible = not DropdownList.Visible
    end)
    
    return DropdownFrame
end

local function CreateESP(player)
    if ESPObjects[player] then return end
    
    local esp = {}
    
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local highlight = Instance.new("Highlight")
        highlight.Name = "ESP_Highlight"
        highlight.Parent = player.Character
        highlight.Adornee = player.Character
        highlight.FillColor = player.Team == LocalPlayer.Team and Color3.fromRGB(0, 100, 255) or Color3.fromRGB(255, 50, 50)
        highlight.FillTransparency = 0.8
        highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
        highlight.OutlineTransparency = 0
        
        esp.Highlight = highlight
        ESPObjects[player] = esp
    end
end

local function RemoveESP(player)
    if ESPObjects[player] then
        if ESPObjects[player].Highlight then
            ESPObjects[player].Highlight:Destroy()
        end
        ESPObjects[player] = nil
    end
end

local function UpdateESP()
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            if Utilities.ESP.Enabled then
                CreateESP(player)
            else
                RemoveESP(player)
            end
        end
    end
end

local function ToggleNoclip()
    if Utilities.Noclip.Enabled then
        NoclipConnection = RunService.Stepped:Connect(function()
            if not Utilities.Noclip.Enabled then return end
            
            local character = LocalPlayer.Character
            if not character then return end
            
            for _, part in pairs(character:GetDescendants()) do
                if part:IsA("BasePart") and part.CanCollide then
                    part.CanCollide = false
                end
            end
        end)
        
        NoclipStatus.Text = "NOCLIP: ON"
        NoclipStatus.TextColor3 = Color3.fromRGB(50, 255, 50)
    else
        if NoclipConnection then
            NoclipConnection:Disconnect()
            NoclipConnection = nil
        end
        
        local character = LocalPlayer.Character
        if character then
            for _, part in pairs(character:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = true
                end
            end
        end
        
        NoclipStatus.Text = "NOCLIP: OFF"
        NoclipStatus.TextColor3 = Color3.fromRGB(255, 50, 50)
    end
end

local function GetClosestPlayer()
    local closest = nil
    local minDist = Utilities.Aimlock.MaxDistance
    local root = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not root then return nil end
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character then
            local hrp = player.Character:FindFirstChild("HumanoidRootPart")
            local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
            
            if hrp and humanoid and humanoid.Health > 0 then
                local distance = (root.Position - hrp.Position).Magnitude
                if distance < minDist then
                    minDist = distance
                    closest = player
                end
            end
        end
    end
    
    return closest
end

local AimlockConnection
local function AimlockLoop()
    if not Utilities.Aimlock.Enabled then return end
    
    local target = GetClosestPlayer()
    if not target or not target.Character then
        AimlockStatus.Text = "AIMLOCK: NO TARGET"
        AimlockStatus.TextColor3 = Color3.fromRGB(255, 255, 50)
        TargetStatus.Text = "TARGET: NONE"
        return
    end
    
    Utilities.Aimlock.Target = target
    
    local targetPart = nil
    if Utilities.Aimlock.BodyPart ~= "None" then
        targetPart = target.Character:FindFirstChild(Utilities.Aimlock.BodyPart)
    end
    
    if not targetPart then
        targetPart = target.Character:FindFirstChild("HumanoidRootPart")
    end
    
    if not targetPart then return end
    
    local camera = Camera
    local currentCFrame = camera.CFrame
    local targetPosition = targetPart.Position + (targetPart.Velocity * Utilities.Aimlock.Prediction)
    
    local effectiveSmoothing = Utilities.Aimlock.Smoothing * Utilities.Aimlock.Sensitivity
    local goalCFrame = CFrame.new(currentCFrame.Position, targetPosition)
    camera.CFrame = currentCFrame:Lerp(goalCFrame, effectiveSmoothing)
    
    AimlockStatus.Text = "AIMLOCK: ON"
    AimlockStatus.TextColor3 = Color3.fromRGB(50, 255, 50)
    TargetStatus.Text = "TARGET: " .. target.Name .. " [" .. Utilities.Aimlock.BodyPart .. "]"
end

local function ToggleAimlock()
    Utilities.Aimlock.Enabled = not Utilities.Aimlock.Enabled
    
    if Utilities.Aimlock.Enabled then
        if AimlockConnection then AimlockConnection:Disconnect() end
        AimlockConnection = RunService.RenderStepped:Connect(AimlockLoop)
    else
        if AimlockConnection then 
            AimlockConnection:Disconnect() 
            AimlockConnection = nil 
        end
        AimlockStatus.Text = "AIMLOCK: OFF"
        AimlockStatus.TextColor3 = Color3.fromRGB(255, 50, 50)
        TargetStatus.Text = "TARGET: NONE"
    end
end

CreateToggle("AIMLOCK [F]", UDim2.new(0, 10, 0, 10), "Aimlock", ToggleAimlock)
CreateToggle("NOCLIP [G]", UDim2.new(0, 10, 0, 50), "Noclip", ToggleNoclip)
CreateToggle("ESP [H]", UDim2.new(0, 10, 0, 90), "ESP", function(enabled)
    ESPStatus.Text = "ESP: " .. (enabled and "ON" or "OFF")
    ESPStatus.TextColor3 = enabled and Color3.fromRGB(50, 255, 50) or Color3.fromRGB(255, 50, 50)
    UpdateESP()
end)

CreateSlider("SENSIBILIDADE", UDim2.new(0, 10, 0, 130), 0.1, 1.0, Utilities.Aimlock.Sensitivity, function(value)
    Utilities.Aimlock.Sensitivity = value
end)

CreateSlider("DISTÂNCIA MAX", UDim2.new(0, 10, 0, 180), 10, 200, Utilities.Aimlock.MaxDistance, function(value)
    Utilities.Aimlock.MaxDistance = value
end)

CreateSlider("PREDIÇÃO", UDim2.new(0, 10, 0, 230), 0.0, 0.5, Utilities.Aimlock.Prediction, function(value)
    Utilities.Aimlock.Prediction = value
end)

CreateBodyPartDropdown()

UIS.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    
    if input.KeyCode == Utilities.Aimlock.Key then
        ToggleAimlock()
    elseif input.KeyCode == Utilities.Noclip.Key then
        Utilities.Noclip.Enabled = not Utilities.Noclip.Enabled
        ToggleNoclip()
    elseif input.KeyCode == Utilities.ESP.Key then
        Utilities.ESP.Enabled = not Utilities.ESP.Enabled
        ESPStatus.Text = "ESP: " .. (Utilities.ESP.Enabled and "ON" or "OFF")
        ESPStatus.TextColor3 = Utilities.ESP.Enabled and Color3.fromRGB(50, 255, 50) or Color3.fromRGB(255, 50, 50)
        UpdateESP()
    end
end)

LocalPlayer.CharacterAdded:Connect(function(character)
    wait(1)
    if Utilities.Noclip.Enabled then
        ToggleNoclip()
    end
end)

Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function()
        if Utilities.ESP.Enabled then
            wait(1)
            CreateESP(player)
        end
    end)
end)

Players.PlayerRemoving:Connect(function(player)
    RemoveESP(player)
end)

for _, player in pairs(Players:GetPlayers()) do
    if player ~= LocalPlayer and Utilities.ESP.Enabled then
        CreateESP(player)
    end
end

print("🚀 YEV HUB v2.2 CARREGADO!")
print("📌 CONTROLES:")
print("   F - Aimlock | G - Noclip | H - ESP")
print("🎯 CONFIGURAÇÕES AIMLOCK:")
print("   - Escolha onde mirar: Head, Torso, HumanoidRootPart ou None")
print("   - Ajuste distância máxima: " .. Utilities.Aimlock.MaxDistance)
print("   - Controle sensibilidade: " .. Utilities.Aimlock.Sensitivity)
print("   - Configure predição: " .. Utilities.Aimlock.Prediction)
print("✅ Sistema totalmente customizável!")
