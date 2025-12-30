-- Auto Click Favorite Items GUI Fixed
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- Konfigurasi
local autoClickEnabled = false
local clickDelay = 0.5

local itemsToFavorite = {
    "Octobloom Seed ",
    "Zebrazinkle Seed "
}

-- Buat GUI
local ScreenGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local Title = Instance.new("TextLabel")
local ToggleButton = Instance.new("TextButton")
local MinimizeButton = Instance.new("TextButton")
local StatusLabel = Instance.new("TextLabel")

-- Coba parent ke PlayerGui dulu, kalau gagal baru ke CoreGui
pcall(function()
    ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
end)
if not ScreenGui.Parent then
    ScreenGui.Parent = game.CoreGui
end
ScreenGui.ResetOnSpawn = false

-- Main Frame
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
MainFrame.BorderSizePixel = 0
MainFrame.Position = UDim2.new(0.35, 0, 0.3, 0)
MainFrame.Size = UDim2.new(0, 280, 0, 150)
MainFrame.Active = true
MainFrame.Draggable = true

local Corner = Instance.new("UICorner")
Corner.CornerRadius = UDim.new(0, 10)
Corner.Parent = MainFrame

-- Title
Title.Parent = MainFrame
Title.BackgroundTransparency = 1
Title.Position = UDim2.new(0, 0, 0, 0)
Title.Size = UDim2.new(1, 0, 0, 35)
Title.Font = Enum.Font.GothamBold
Title.Text = "🌸 Auto Favorite"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 16

-- Toggle Button
ToggleButton.Parent = MainFrame
ToggleButton.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
ToggleButton.Position = UDim2.new(0.1, 0, 0.3, 0)
ToggleButton.Size = UDim2.new(0.8, 0, 0, 35)
ToggleButton.Font = Enum.Font.GothamBold
ToggleButton.Text = "▶ START"
ToggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleButton.TextSize = 14

local ToggleCorner = Instance.new("UICorner")
ToggleCorner.CornerRadius = UDim.new(0, 8)
ToggleCorner.Parent = ToggleButton

-- Status Label
StatusLabel.Parent = MainFrame
StatusLabel.BackgroundTransparency = 1
StatusLabel.Position = UDim2.new(0, 0, 0.6, 0)
StatusLabel.Size = UDim2.new(1, 0, 0, 25)
StatusLabel.Font = Enum.Font.Gotham
StatusLabel.Text = "⚫ Status: OFF"
StatusLabel.TextColor3 = Color3.fromRGB(220, 53, 69)
StatusLabel.TextSize = 13

-- Minimize Button
MinimizeButton.Parent = MainFrame
MinimizeButton.BackgroundColor3 = Color3.fromRGB(255, 193, 7)
MinimizeButton.Position = UDim2.new(0.3, 0, 0.8, 0)
MinimizeButton.Size = UDim2.new(0.4, 0, 0, 25)
MinimizeButton.Font = Enum.Font.GothamBold
MinimizeButton.Text = "━"
MinimizeButton.TextColor3 = Color3.fromRGB(0, 0, 0)
MinimizeButton.TextSize = 14

local MinCorner = Instance.new("UICorner")
MinCorner.CornerRadius = UDim.new(0, 6)
MinCorner.Parent = MinimizeButton

-- Fungsi favorite item dengan error handling
local function favoriteItem(itemName)
    pcall(function()
        local character = LocalPlayer.Character
        local backpack = LocalPlayer.Backpack
        
        -- Cek di backpack
        local item = backpack:FindFirstChild(itemName)
        
        -- Kalau tidak ada di backpack, cek di character
        if not item and character then
            item = character:FindFirstChild(itemName)
        end
        
        if item then
            ReplicatedStorage.GameEvents.Favorite_Item:FireServer(item)
            print("✓ Favorited: " .. itemName)
        else
            warn("✗ Item not found: " .. itemName)
        end
    end)
end

-- Auto click loop
local loopRunning = false
local function autoClickLoop()
    loopRunning = true
    while autoClickEnabled and loopRunning do
        for _, itemName in ipairs(itemsToFavorite) do
            if not autoClickEnabled then break end
            favoriteItem(itemName)
            task.wait(clickDelay)
        end
        task.wait(1)
    end
    loopRunning = false
end

-- Toggle button click
ToggleButton.MouseButton1Click:Connect(function()
    autoClickEnabled = not autoClickEnabled
    
    if autoClickEnabled then
        ToggleButton.Text = "■ STOP"
        ToggleButton.BackgroundColor3 = Color3.fromRGB(40, 167, 69)
        StatusLabel.Text = "🟢 Status: ON"
        StatusLabel.TextColor3 = Color3.fromRGB(40, 167, 69)
        
        if not loopRunning then
            task.spawn(autoClickLoop)
        end
    else
        ToggleButton.Text = "▶ START"
        ToggleButton.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
        StatusLabel.Text = "⚫ Status: OFF"
        StatusLabel.TextColor3 = Color3.fromRGB(220, 53, 69)
    end
end)

-- Minimize button click
local isMinimized = false
local originalSize = MainFrame.Size
local minimizedSize = UDim2.new(0, 280, 0, 35)

MinimizeButton.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    
    if isMinimized then
        MainFrame:TweenSize(minimizedSize, "Out", "Quad", 0.3, true)
        task.wait(0.3)
        ToggleButton.Visible = false
        StatusLabel.Visible = false
        MinimizeButton.Text = "□"
    else
        MainFrame:TweenSize(originalSize, "Out", "Quad", 0.3, true)
        ToggleButton.Visible = true
        StatusLabel.Visible = true
        MinimizeButton.Text = "━"
    end
end)

-- Click title untuk maximize
Title.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 and isMinimized then
        isMinimized = false
        MainFrame:TweenSize(originalSize, "Out", "Quad", 0.3, true)
        ToggleButton.Visible = true
        StatusLabel.Visible = true
        MinimizeButton.Text = "━"
    end
end)

print("✅ Auto Favorite GUI Loaded Successfully!")
