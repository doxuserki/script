local Players          = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer      = Players.LocalPlayer
local PlayerGui        = LocalPlayer.PlayerGui

-- Helper
local function create(class, props, parent)
    local obj = Instance.new(class)
    for k, v in pairs(props) do obj[k] = v end
    obj.Parent = parent
    return obj
end

local function corner(radius, parent)
    create("UICorner", {CornerRadius = UDim.new(0, radius)}, parent)
end

-- Nettoie l'ancien GUI
local old = PlayerGui:FindFirstChild("InstaKickGui")
if old then old:Destroy() end

-- ScreenGui
local screenGui = create("ScreenGui", {
    Name = "InstaKickGui",
    ResetOnSpawn = false,
    IgnoreGuiInset = true,
    ZIndexBehavior = Enum.ZIndexBehavior.Sibling,
}, PlayerGui)

-- Outer glow
local glowFrame = create("Frame", {
    Size = UDim2.new(0, 224, 0, 155),
    Position = UDim2.new(0, 150, 0, 70),
    AnchorPoint = Vector2.new(0, 0),
    BackgroundColor3 = Color3.fromRGB(124, 58, 237),
    BorderSizePixel = 0,
    ClipsDescendants = true,
}, screenGui)
corner(16, glowFrame)

-- Inner frame
local mainFrame = create("Frame", {
    Size = UDim2.new(1, -4, 1, -4),
    Position = UDim2.new(0, 2, 0, 2),
    BackgroundColor3 = Color3.fromRGB(13, 13, 26),
    BorderSizePixel = 0,
    ClipsDescendants = true,
}, glowFrame)
corner(14, mainFrame)

-- Top line
create("Frame", {
    Size = UDim2.new(0.7, 0, 0, 1),
    Position = UDim2.new(0.15, 0, 0, 0),
    BackgroundColor3 = Color3.fromRGB(96, 165, 250),
    BorderSizePixel = 0,
}, mainFrame)

-- Labels
create("TextLabel", {
    Size = UDim2.new(1, 0, 0, 30), Position = UDim2.new(0, 0, 0, 10),
    BackgroundTransparency = 1, Text = "INSTA-KICK",
    TextColor3 = Color3.fromRGB(167, 139, 250), TextSize = 19,
    Font = Enum.Font.GothamBold,
}, mainFrame)

create("TextLabel", {
    Size = UDim2.new(1, 0, 0, 16), Position = UDim2.new(0, 0, 0, 38),
    BackgroundTransparency = 1, Text = "by fsk",
    TextColor3 = Color3.fromRGB(96, 165, 250), TextSize = 13,
    Font = Enum.Font.Gotham,
}, mainFrame)

create("TextLabel", {
    Size = UDim2.new(1, 0, 0, 14), Position = UDim2.new(0, 0, 0, 52),
    BackgroundTransparency = 1, Text = "@hw9l",
    TextColor3 = Color3.fromRGB(109, 40, 217), TextSize = 11,
    Font = Enum.Font.Gotham,
}, mainFrame)

-- Leave button
local btnOuter = create("Frame", {
    Size = UDim2.new(1, -20, 0, 36), Position = UDim2.new(0, 10, 0, 72),
    BackgroundColor3 = Color3.fromRGB(109, 40, 217), BorderSizePixel = 0,
}, mainFrame)
corner(10, btnOuter)

local leaveButton = create("TextButton", {
    Size = UDim2.new(1, -4, 1, -4), Position = UDim2.new(0, 2, 0, 2),
    BackgroundColor3 = Color3.fromRGB(13, 0, 24), Text = "LEAVE GAME",
    TextColor3 = Color3.fromRGB(167, 139, 250), TextSize = 14,
    Font = Enum.Font.GothamBold, BorderSizePixel = 0, AutoButtonColor = false,
}, btnOuter)
corner(8, leaveButton)

-- Toggle button
local toggleOuter = create("Frame", {
    Size = UDim2.new(1, -20, 0, 36), Position = UDim2.new(0, 10, 0, 114),
    BackgroundColor3 = Color3.fromRGB(80, 80, 80), BorderSizePixel = 0,
}, mainFrame)
corner(10, toggleOuter)

local toggleButton = create("TextButton", {
    Size = UDim2.new(1, -4, 1, -4), Position = UDim2.new(0, 2, 0, 2),
    BackgroundColor3 = Color3.fromRGB(30, 30, 30), Text = "AUTO-KICK  |  OFF",
    TextColor3 = Color3.fromRGB(150, 150, 150), TextSize = 13,
    Font = Enum.Font.GothamBold, BorderSizePixel = 0, AutoButtonColor = false,
}, toggleOuter)
corner(8, toggleButton)

-- ══════════════════════════
--          DRAG
-- ══════════════════════════

local dragging, dragInput, dragStart, startPos

glowFrame.InputBegan:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = i.Position
        startPos = glowFrame.Position
        i.Changed:Connect(function()
            if i.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

glowFrame.InputChanged:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseMovement then
        dragInput = i
    end
end)

UserInputService.InputChanged:Connect(function(i)
    if i == dragInput and dragging then
        local d = i.Position - dragStart
        glowFrame.Position = UDim2.new(
            startPos.X.Scale, startPos.X.Offset + d.X,
            startPos.Y.Scale, startPos.Y.Offset + d.Y
        )
    end
end)

-- ══════════════════════════
--     FONCTION KICK
-- ══════════════════════════

local kicked = false

local function doKick()
    if kicked then return end
    kicked = true
    leaveButton.BackgroundColor3 = Color3.fromRGB(0, 60, 0)
    leaveButton.TextColor3       = Color3.fromRGB(74, 222, 128)
    leaveButton.Text             = "KICKED!"
    task.delay(0, function()
        LocalPlayer:Kick("kicked by fsk")
    end)
end

-- ══════════════════════════
--        BOUTON LEAVE
-- ══════════════════════════

leaveButton.MouseButton1Click:Connect(doKick)

leaveButton.MouseEnter:Connect(function()
    if not kicked then
        leaveButton.BackgroundColor3 = Color3.fromRGB(45, 0, 112)
        leaveButton.TextColor3       = Color3.fromRGB(255, 255, 255)
    end
end)

leaveButton.MouseLeave:Connect(function()
    if not kicked then
        leaveButton.BackgroundColor3 = Color3.fromRGB(13, 0, 24)
        leaveButton.TextColor3       = Color3.fromRGB(167, 139, 250)
    end
end)

-- ══════════════════════════
--       TOGGLE AUTO-KICK
-- ══════════════════════════

local autoKickEnabled = false

toggleButton.MouseButton1Click:Connect(function()
    autoKickEnabled = not autoKickEnabled
    if autoKickEnabled then
        toggleOuter.BackgroundColor3  = Color3.fromRGB(0, 100, 0)
        toggleButton.BackgroundColor3 = Color3.fromRGB(0, 40, 0)
        toggleButton.TextColor3       = Color3.fromRGB(74, 222, 128)
        toggleButton.Text             = "AUTO-KICK  |  ON"
    else
        toggleOuter.BackgroundColor3  = Color3.fromRGB(80, 80, 80)
        toggleButton.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
        toggleButton.TextColor3       = Color3.fromRGB(150, 150, 150)
        toggleButton.Text             = "AUTO-KICK  |  OFF"
    end
end)

-- ══════════════════════════
--   AUTO-KICK APRÈS VOL
-- ══════════════════════════

LocalPlayer:GetAttributeChangedSignal("Stealing"):Connect(function()
    if not autoKickEnabled then return end
    local stealing      = LocalPlayer:GetAttribute("Stealing")
    local stealingIndex = LocalPlayer:GetAttribute("StealingIndex")
    if stealing == nil and (stealingIndex == nil or stealingIndex == "") then
        doKick()
    end
end)
