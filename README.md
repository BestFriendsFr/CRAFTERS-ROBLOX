# CRAFTERS-ROBLOX
This script is just an esp and speed boost 😋
--[[\
    PC Game Utility Suite (Zero-Lag Event-Driven Version)
]]--

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local CoreGui = game:GetService("CoreGui")
local LocalPlayer = Players.LocalPlayer

-- Prevent duplicate UI instances
if CoreGui:FindFirstChild("PCGameUtilityDrawer") then
    CoreGui.PCGameUtilityDrawer:Destroy()
end

-- ScreenGui Setup
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "PCGameUtilityDrawer"
ScreenGui.Parent = CoreGui
ScreenGui.ResetOnSpawn = false
ScreenGui.DisplayOrder = 99999

-- Main Frame (PC Sized Window)
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(22, 22, 28)
MainFrame.BorderSizePixel = 0
MainFrame.Position = UDim2.new(0.05, 0, 0.15, 0)
MainFrame.Size = UDim2.new(0, 240, 0, 440)
MainFrame.Active = true
MainFrame.Draggable = true

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 8)
UICorner.Parent = MainFrame

-- Top Bar
local TopBar = Instance.new("Frame")
TopBar.Name = "TopBar"
TopBar.Parent = MainFrame
TopBar.BackgroundColor3 = Color3.fromRGB(32, 32, 40)
TopBar.BorderSizePixel = 0
TopBar.Size = UDim2.new(1, 0, 0, 36)

local TopCorner = Instance.new("UICorner")
TopCorner.CornerRadius = UDim.new(0, 8)
TopCorner.Parent = TopBar

local Title = Instance.new("TextLabel")
Title.Parent = TopBar
Title.BackgroundTransparency = 1
Title.Position = UDim2.new(0.05, 0, 0, 0)
Title.Size = UDim2.new(0.7, 0, 1, 0)
Title.Font = Enum.Font.GothamBold
Title.Text = "PC Utility Menu (RightShift: Hide)"
Title.TextColor3 = Color3.fromRGB(240, 240, 240)
Title.TextSize = 10
Title.TextXAlignment = Enum.TextXAlignment.Left

-- Top Controls: (-) Minimize, (×) Hide
local CloseBtn = Instance.new("TextButton")
CloseBtn.Parent = TopBar
CloseBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
CloseBtn.Position = UDim2.new(1, -28, 0.5, -10)
CloseBtn.Size = UDim2.new(0, 20, 0, 20)
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.Text = "×"
CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseBtn.TextSize = 13
Instance.new("UICorner", CloseBtn).CornerRadius = UDim.new(0, 4)

local MinBtn = Instance.new("TextButton")
MinBtn.Parent = TopBar
MinBtn.BackgroundColor3 = Color3.fromRGB(75, 75, 85)
MinBtn.Position = UDim2.new(1, -54, 0.5, -10)
MinBtn.Size = UDim2.new(0, 20, 0, 20)
MinBtn.Font = Enum.Font.GothamBold
MinBtn.Text = "-"
MinBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
MinBtn.TextSize = 13
Instance.new("UICorner", MinBtn).CornerRadius = UDim.new(0, 4)

-- Scrolling Container for Features
local ContentContainer = Instance.new("ScrollingFrame")
ContentContainer.Parent = MainFrame
ContentContainer.BackgroundTransparency = 1
ContentContainer.Position = UDim2.new(0, 0, 0, 40)
ContentContainer.Size = UDim2.new(1, 0, 1, -40)
ContentContainer.CanvasSize = UDim2.new(0, 0, 0, 560)
ContentContainer.ScrollBarThickness = 4

local UIListLayout = Instance.new("UIListLayout")
UIListLayout.Parent = ContentContainer
UIListLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
UIListLayout.Padding = UDim.new(0, 8)

-- State Variables
local espGrimEnabled = false
local espObjectsEnabled = false
local espBananaEnabled = false
local espCoinEnabled = false
local espCardEnabled = false
local espSweetboxEnabled = false
local speedEnabled = false
local speedValue = 28

-- Helper: Create PC Toggle Button
local function createToggle(name, callback)
    local btn = Instance.new("TextButton")
    btn.Parent = ContentContainer
    btn.BackgroundColor3 = Color3.fromRGB(42, 42, 52)
    btn.Size = UDim2.new(0, 220, 0, 32)
    btn.Font = Enum.Font.GothamMedium
    btn.Text = name .. ": OFF"
    btn.TextColor3 = Color3.fromRGB(200, 200, 200)
    btn.TextSize = 12
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)

    local state = false
    btn.MouseButton1Click:Connect(function()
        state = not state
        btn.Text = name .. (state and ": ON" or ": OFF")
        btn.BackgroundColor3 = state and Color3.fromRGB(45, 130, 85) or Color3.fromRGB(42, 42, 52)
        btn.TextColor3 = state and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(200, 200, 200)
        pcall(function() callback(state) end)
    end)
    return btn
end

-- Helper: Create PC Speed Slider
local function createSlider(name, min, max, default, callback)
    local frame = Instance.new("Frame")
    frame.Parent = ContentContainer
    frame.BackgroundColor3 = Color3.fromRGB(36, 36, 46)
    frame.Size = UDim2.new(0, 220, 0, 46)
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 6)

    local label = Instance.new("TextLabel")
    label.Parent = frame
    label.BackgroundTransparency = 1
    label.Position = UDim2.new(0, 10, 0, 4)
    label.Size = UDim2.new(1, -20, 0, 18)
    label.Font = Enum.Font.GothamMedium
    label.Text = name .. ": " .. tostring(default)
    label.TextColor3 = Color3.fromRGB(200, 200, 200)
    label.TextSize = 11
    label.TextXAlignment = Enum.TextXAlignment.Left

    local sliderBar = Instance.new("TextButton")
    sliderBar.Parent = frame
    sliderBar.BackgroundColor3 = Color3.fromRGB(55, 55, 65)
    sliderBar.Position = UDim2.new(0, 10, 0, 26)
    sliderBar.Size = UDim2.new(1, -20, 0, 12)
    sliderBar.Text = ""
    Instance.new("UICorner", sliderBar).CornerRadius = UDim.new(0, 4)

    local fill = Instance.new("Frame")
    fill.Parent = sliderBar
    fill.BackgroundColor3 = Color3.fromRGB(70, 150, 255)
    fill.Size = UDim2.new((default - min)/(max - min), 0, 1, 0)
    Instance.new("UICorner", fill).CornerRadius = UDim.new(0, 4)

    local dragging = false
    local function updateInput(input)
        local pos = math.clamp((input.Position.X - sliderBar.AbsolutePosition.X) / sliderBar.AbsoluteSize.X, 0, 1)
        fill.Size = UDim2.new(pos, 0, 1, 0)
        local val = math.floor(min + (max - min) * pos)
        label.Text = name .. ": " .. tostring(val)
        pcall(function() callback(val) end)
    end

    sliderBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            updateInput(input)
        end
    end)
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            updateInput(input)
        end
    end)
end

-- Floating Re-open Button
local OpenBtn = Instance.new("TextButton")
OpenBtn.Name = "OpenButton"
OpenBtn.Parent = ScreenGui
OpenBtn.BackgroundColor3 = Color3.fromRGB(28, 28, 36)
OpenBtn.Position = UDim2.new(0.01, 0, 0.05, 0)
OpenBtn.Size = UDim2.new(0, 40, 0, 40)
OpenBtn.Font = Enum.Font.GothamBold
OpenBtn.Text = "📂"
OpenBtn.TextSize = 18
OpenBtn.Visible = false
Instance.new("UICorner", OpenBtn).CornerRadius = UDim.new(0, 6)

CloseBtn.MouseButton1Click:Connect(function()
    MainFrame.Visible = false
    OpenBtn.Visible = true
end)

OpenBtn.MouseButton1Click:Connect(function()
    MainFrame.Visible = true
    OpenBtn.Visible = false
end)

local minimized = false
MinBtn.MouseButton1Click:Connect(function()
    minimized = not minimized
    ContentContainer.Visible = not minimized
    MainFrame.Size = minimized and UDim2.new(0, 240, 0, 36) or UDim2.new(0, 240, 0, 440)
end)

-- Keyboard Toggle (Right Shift to hide/show menu)
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed and input.KeyCode == Enum.KeyCode.RightShift then
        MainFrame.Visible = not MainFrame.Visible
        OpenBtn.Visible = not MainFrame.Visible
    end
end)

----------------------------------------------------
-- FEATURE MODULES & ZERO-LAG EVENT LOGIC
----------------------------------------------------

local function processHighlight(obj)
    if not (obj:IsA("Model") or obj:IsA("BasePart")) then return end
    local nameLower = string.lower(obj.Name)

    -- Grimcrafters ESP
    if espGrimEnabled and (string.find(nameLower, "grimcrafter") or string.find(nameLower, "enemy")) then
        if not obj:FindFirstChild("GrimESP") then
            local hl = Instance.new("Highlight")
            hl.Name = "GrimESP"
            hl.Adornee = obj
            hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
            hl.FillColor = Color3.fromRGB(255, 50, 50)
            hl.OutlineColor = Color3.fromRGB(255, 255, 255)
            hl.FillTransparency = 0.5
            hl.OutlineTransparency = 0
            hl.Parent = obj
        end
    end

    -- Rituals/Stones ESP
    if espObjectsEnabled and (string.find(nameLower, "ritual") or string.find(nameLower, "totem") or string.find(nameLower, "purifier") or string.find(nameLower, "stone") or string.find(nameLower, "dark")) then
        if not obj:FindFirstChild("ObjectESP") then
            local hl = Instance.new("Highlight")
            hl.Name = "ObjectESP"
            hl.Adornee = obj
            hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
            hl.FillColor = Color3.fromRGB(0, 255, 255)
            hl.OutlineColor = Color3.fromRGB(255, 255, 255)
            hl.FillTransparency = 0.5
            hl.OutlineTransparency = 0
            hl.Parent = obj
        end
    end

    -- Bananas ESP
    if espBananaEnabled and string.find(nameLower, "banana") then
        if not obj:FindFirstChild("BananaESP") then
            local hl = Instance.new("Highlight")
            hl.Name = "BananaESP"
            hl.Adornee = obj
            hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
            hl.FillColor = Color3.fromRGB(255, 225, 0)
            hl.OutlineColor = Color3.fromRGB(255, 255, 255)
            hl.FillTransparency = 0.5
            hl.OutlineTransparency = 0
            hl.Parent = obj
        end
    end

    -- Craft Coins ESP (Silver/White Glow)
    if espCoinEnabled and (string.find(nameLower, "craft coin") or string.find(nameLower, "coin") or string.find(nameLower, "currency")) then
        if not obj:FindFirstChild("CoinESP") then
            local hl = Instance.new("Highlight")
            hl.Name = "CoinESP"
            hl.Adornee = obj
            hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
            hl.FillColor = Color3.fromRGB(220, 220, 240)
            hl.OutlineColor = Color3.fromRGB(255, 255, 255)
            hl.FillTransparency = 0.4
            hl.OutlineTransparency = 0
            hl.Parent = obj
        end
    end

    -- Cards ESP (Mystic Blue Glow)
    if espCardEnabled and (string.find(nameLower, "card") or string.find(nameLower, "deck")) then
        if not obj:FindFirstChild("CardESP") then
            local hl = Instance.new("Highlight")
            hl.Name = "CardESP"
            hl.Adornee = obj
            hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
            hl.FillColor = Color3.fromRGB(50, 120, 255)
            hl.OutlineColor = Color3.fromRGB(255, 255, 255)
            hl.FillTransparency = 0.4
            hl.OutlineTransparency = 0
            hl.Parent = obj
        end
    end

    -- Sweetbox (Heal) ESP (Soft Pink/Magenta Glow)
    if espSweetboxEnabled and (string.find(nameLower, "sweetbox") or string.find(nameLower, "heal") or string.find(nameLower, "health")) then
        if not obj:FindFirstChild("SweetboxESP") then
            local hl = Instance.new("Highlight")
            hl.Name = "SweetboxESP"
            hl.Adornee = obj
            hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
            hl.FillColor = Color3.fromRGB(255, 105, 180)
            hl.OutlineColor = Color3.fromRGB(255, 255, 255)
            hl.FillTransparency = 0.4
            hl.OutlineTransparency = 0
            hl.Parent = obj
        end
    end
end

local function clearHighlights(name)
    for _, v in pairs(workspace:GetDescendants()) do
        if v:IsA("Highlight") and v.Name == name then
            v:Destroy()
        end
    end
end

createToggle("ESP Grimcrafters", function(state)
    espGrimEnabled = state
    if state then
        for _, obj in pairs(workspace:GetDescendants()) do processHighlight(obj) end
    else
        clearHighlights("GrimESP")
    end
end)

createToggle("ESP Rituals/Stones", function(state)
    espObjectsEnabled = state
    if state then
        for _, obj in pairs(workspace:GetDescendants()) do processHighlight(obj) end
    else
        clearHighlights("ObjectESP")
    end
end)

createToggle("ESP Bananas", function(state)
    espBananaEnabled = state
    if state then
        for _, obj in pairs(workspace:GetDescendants()) do processHighlight(obj) end
    else
        clearHighlights("BananaESP")
    end
end)

createToggle("ESP Craft Coins", function(state)
    espCoinEnabled = state
    if state then
        for _, obj in pairs(workspace:GetDescendants()) do processHighlight(obj) end
    else
        clearHighlights("CoinESP")
    end
end)

createToggle("ESP Cards", function(state)
    espCardEnabled = state
    if state then
        for _, obj in pairs(workspace:GetDescendants()) do processHighlight(obj) end
    else
        clearHighlights("CardESP")
    end
end)

createToggle("ESP Sweetbox", function(state)
    espSweetboxEnabled = state
    if state then
        for _, obj in pairs(workspace:GetDescendants()) do processHighlight(obj) end
    else
        clearHighlights("SweetboxESP")
    end
end)

createToggle("Speed Boost", function(state)
    speedEnabled = state
    if not speedEnabled and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = 16
    end
end)

createSlider("WalkSpeed Value", 16, 150, 28, function(val)
    speedValue = val
end)

-- Speed Loop
RunService.RenderStepped:Connect(function()
    local char = LocalPlayer.Character
    if char then
        local humanoid = char:FindFirstChild("Humanoid")
        if speedEnabled and humanoid then
            humanoid.WalkSpeed = speedValue
        end
    end
end)

-- Event-driven detection (Zero Lag)
workspace.DescendantAdded:Connect(function(obj)
    task.wait(0.1)
    processHighlight(obj)
end)
