local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Players = game:GetService("Players")

local Player = Players.LocalPlayer
local PlayerGui = Player:WaitForChild("PlayerGui")
local EggHitRequest = ReplicatedStorage:WaitForChild("EggHitRequest")

local enabled = true
local isProcessing = false
local currentMultiplier = 4

print("[Damage Multiplier] Activated!")

-- GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "EggMultiplierTopGUI"
ScreenGui.ResetOnSpawn = false

pcall(function()
    ScreenGui.Parent = game:GetService("CoreGui")
end)

if not ScreenGui.Parent then
    ScreenGui.Parent = PlayerGui
end

local Container = Instance.new("Frame")
Container.Name = "MultiplierContainer"
Container.Size = UDim2.new(0, 235, 0, 36)
Container.AnchorPoint = Vector2.new(1, 0)
Container.Position = UDim2.new(1, -10, 0, 10)
Container.BackgroundTransparency = 1
Container.Parent = ScreenGui

local UIListLayout = Instance.new("UIListLayout")
UIListLayout.FillDirection = Enum.FillDirection.Horizontal
UIListLayout.HorizontalAlignment = Enum.HorizontalAlignment.Right
UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
UIListLayout.Padding = UDim.new(0, 5)
UIListLayout.Parent = Container

local multipliers = {1, 2, 3, 4, 5}
local multiButtons = {}

-- MÀU RIÊNG TỪNG NÚT
local buttonColors = {
    [1] = Color3.fromRGB(100, 100, 100), -- Xám
    [2] = Color3.fromRGB(0, 170, 255),   -- Xanh dương
    [3] = Color3.fromRGB(0, 200, 120),   -- Xanh lá
    [4] = Color3.fromRGB(170, 70, 255),  -- Tím
    [5] = Color3.fromRGB(255, 70, 70)    -- Đỏ
}

for _, val in ipairs(multipliers) do
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 40, 0, 32)
    btn.BackgroundColor3 = buttonColors[val]
    btn.BackgroundTransparency = (val == 4) and 0.1 or 0.4
    btn.Text = val .. "x"
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.TextSize = 13
    btn.Font = Enum.Font.GothamBold
    btn.Parent = Container

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 6)
    corner.Parent = btn

    local stroke = Instance.new("UIStroke")
    stroke.Color = Color3.new(1, 1, 1)
    stroke.Thickness = 1.5
    stroke.Parent = btn

    multiButtons[val] = btn

    btn.Activated:Connect(function()
        currentMultiplier = val

        for mVal, button in pairs(multiButtons) do
            button.BackgroundColor3 = buttonColors[mVal]
            button.BackgroundTransparency =
                (mVal == val) and 0.05 or 0.45
        end

        print("[Damage Multiplier] Selected:", val .. "x")
    end)
end

-- HOOK FIRE SERVER
local oldNameCall
oldNameCall = hookmetamethod(game, "__namecall", function(self, ...)
    local method = getnamecallmethod()
    local args = {...}

    if enabled and not isProcessing
        and currentMultiplier > 1
        and self == EggHitRequest
        and method == "FireServer" then

        local eggObject = args[1]
        local baseHit = args[2] or 1

        if eggObject then
            isProcessing = true

            local extraCount = currentMultiplier - 1

            task.spawn(function()
                for i = 1, extraCount do
                    local nextHit = baseHit + i
                    self:FireServer(eggObject, nextHit)
                end

                task.wait(1.0)
                isProcessing = false
            end)
        end
    end

    return oldNameCall(self, ...)
end)

print("[Damage Multiplier] Ready! 1x-5x")
