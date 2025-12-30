-- Auto Click Favorite Items dengan GUI
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

ScreenGui.Parent = game.CoreGui
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

-- Main Frame
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
MainFrame.BorderSizePixel = 0
MainFrame.Position = UDim2.new(0.5, -150, 0.5, -80)
MainFrame.Size = UDim2.new(0, 300, 0, 160)
MainFrame.Active = true
MainFrame.Draggable = true

-- Rounded corners
local Corner = Instance.new("UICorner")
Corner.CornerRadius = UDim.new(0, 10)
Corner.Parent = MainFrame

-- Title
Title.Parent = MainFrame
Title.BackgroundTransparency = 1
Title.Position = UDim2.new(0, 0, 0, 5)
Title.Size = UDim2.new(1, 0, 0, 30)
Title.Font = Enum.Font.GothamBold
Title.Text = "Auto Favorite"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 18

-- Toggle Button
ToggleButton.Parent = MainFrame
ToggleButton.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
ToggleButton.Position = UDim2.new(0.5, -100, 0, 50)
ToggleButton.Size = UDim2.new(0, 200, 0, 40)
ToggleButton.Font = Enum.Font.GothamBold
ToggleButton.Text = "START"
ToggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleButton.TextSize = 16

local ToggleCorner = Instance.new("UICorner")
ToggleCorner.CornerRadius = UDim.new(0, 8)
ToggleCorner.Parent = ToggleButton

-- Status Label
StatusLabel.Parent = MainFrame
StatusLabel.BackgroundTransparency = 1
StatusLabel.Position = UDim2.new(0, 0, 0, 100)
StatusLabel.Size = UDim2.new(1, 0, 0, 25)
StatusLabel.Font = Enum.Font.Gotham
StatusLabel.Text = "Status: OFF"
StatusLabel.TextColor3 = Color3.fromRGB(220, 53, 69)
StatusLabel.TextSize = 14

-- Minimize Button
MinimizeButton.Parent = MainFrame
MinimizeButton.BackgroundColor3 = Color3.fromRGB(255, 193, 7)
MinimizeButton.Position = UDim2.new(0.5, -40, 0, 130)
MinimizeButton.Size = UDim2.new(0, 80, 0, 25)
MinimizeButton.Font = Enum.Font.GothamBold
MinimizeButton.Text = "MINIMIZE"
MinimizeButton.TextColor3 = Color3.fromRGB(0, 0, 0)
MinimizeButton.TextSize = 12

local MinCorner = Instance.new("UICorner")
MinCorner.CornerRadius = UDim.new(0, 6)
MinCorner.Parent = MinimizeButton

-- Fungsi favorite item
local function favoriteItem(itemName)
    local backpack = LocalPlayer.Backpack
    local item = backpack:FindFirstChild(itemName)
    
    if item then
        ReplicatedStorage.GameEvents.Favorite_Item:FireServer(item)
        return true
    end
    return false
end

-- Auto click loop
local function autoClickLoop()
    while autoClickEnabled do
        for _, itemName in ipairs(itemsToFavorite) do
            favoriteItem(itemName)
            wait(clickDelay)
        end
        wait(1)
    end
end

-- Toggle button click
ToggleButton.MouseButton1Click:Connect(function()
    autoClickEnabled = not autoClickEnabled
    
    if autoClickEnabled then
        ToggleButton.Text = "STOP"
        ToggleButton.BackgroundColor3 = Color3.fromRGB(40, 167, 69)
        StatusLabel.Text = "Status: ON"
        StatusLabel.TextColor3 = Color3.fromRGB(40, 167, 69)
        spawn(autoClickLoop)
    else
        ToggleButton.Text = "START"
        ToggleButton.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
        StatusLabel.Text = "Status: OFF"
        StatusLabel.TextColor3 = Color3.fromRGB(220, 53, 69)
    end
end)

-- Minimize button click
local isMinimized = false
MinimizeButton.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    
    if isMinimized then
        MainFrame.Size = UDim2.new(0, 300, 0, 40)
        MinimizeButton.Visible = false
        ToggleButton.Visible = false
        StatusLabel.Visible = false
    else
        MainFrame.Size = UDim2.new(0, 300, 0, 160)
        MinimizeButton.Visible = true
        ToggleButton.Visible = true
        StatusLabel.Visible = true
    end
end)

-- Double click title to toggle minimize
Title.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        wait(0.3)
        if isMinimized then
            MainFrame.Size = UDim2.new(0, 300, 0, 160)
            MinimizeButton.Visible = true
            ToggleButton.Visible = true
            StatusLabel.Visible = true
            isMinimized = false
        else
            MainFrame.Size = UDim2.new(0, 300, 0, 40)
            MinimizeButton.Visible = false
            ToggleButton.Visible = false
            StatusLabel.Visible = false
            isMinimized = true
        end
    end
end)

print("Auto Favorite GUI Loaded!")
