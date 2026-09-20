--[[
    ╔══════════════════════════════════════════════════╗
    ║   🧴 ROLL-ON HUB v1.0                            ║
    ║   Blox Fruits • Sem Key                          ║
    ║   Inspirado no estilo Redz V2                    ║
    ╚══════════════════════════════════════════════════╝
--]]

-- ═══════════════════════════════════════════
-- SERVIÇOS
-- ═══════════════════════════════════════════
local Players           = game:GetService("Players")
local RunService        = game:GetService("RunService")
local UserInputService  = game:GetService("UserInputService")
local TweenService      = game:GetService("TweenService")
local Workspace         = game:GetService("Workspace")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local CoreGui           = game:GetService("CoreGui")
local VirtualUser       = game:GetService("VirtualUser")

local LP     = Players.LocalPlayer
local Camera = Workspace.CurrentCamera

-- ═══════════════════════════════════════════
-- CONFIG
-- ═══════════════════════════════════════════
getgenv().RollOn = {
    AutoFarm          = false,
    AutoFarmLevel     = false,
    AutoQuest         = false,
    FastAttack        = true,
    AutoBuso          = false,
    AutoCollectFruits = false,
    FruitNotifier     = false,
    AutoChest         = false,
    AutoSwordFarm     = false,
    AutoGunFarm       = false,
    AutoAccessoryFarm = false,
    AutoSeaEvent      = false,
    SeaBeastFarm      = false,
    AutoRaid          = false,
    SpeedHack         = false,
    SpeedValue        = 60,
    Fly               = false,
    FlySpeed          = 80,
    Noclip            = false,
    TweenSpeed        = 350,
    FarmRadius        = 200,
    SelectedSlot      = "Melee",
    UIScale           = 1,
}
local BF = getgenv().RollOn

-- ═══════════════════════════════════════════
-- CORES
-- ═══════════════════════════════════════════
local C = {
    BG       = Color3.fromRGB(15, 15, 22),
    Panel    = Color3.fromRGB(22, 22, 32),
    PanelAlt = Color3.fromRGB(28, 28, 40),
    Accent   = Color3.fromRGB(0, 180, 255),
    Accent2  = Color3.fromRGB(100, 220, 255),
    Text     = Color3.fromRGB(240, 240, 250),
    TextDim  = Color3.fromRGB(150, 150, 170),
    Green    = Color3.fromRGB(60, 220, 130),
    Red      = Color3.fromRGB(240, 70, 100),
}

-- ═══════════════════════════════════════════
-- UTILS
-- ═══════════════════════════════════════════
local function GetChar()
    local c = LP.Character
    if not c or not c:FindFirstChild("HumanoidRootPart") or not c:FindFirstChildOfClass("Humanoid") then return nil end
    return c
end
local function GetHRP()
    local c = GetChar()
    return c and c.HumanoidRootPart
end

-- Tween teleport (anti-detection)
local function TweenTP(targetCFrame)
    local hrp = GetHRP()
    if not hrp or not targetCFrame then return end
    local dist = (hrp.Position - targetCFrame.Position).Magnitude
    if dist < 3 then hrp.CFrame = targetCFrame; return end
    local speed = BF.TweenSpeed
    local time = dist / speed
    local tween = TweenService:Create(hrp, TweenInfo.new(time, Enum.EasingStyle.Linear), { CFrame = targetCFrame })
    tween:Play()
end

-- Notificação
local function Notify(title, text, duration)
    duration = duration or 3
    local pg = LP:WaitForChild("PlayerGui")
    local n = Instance.new("Frame")
    n.Size = UDim2.new(0, 320, 0, 62)
    n.Position = UDim2.new(0, -350, 0, 20)
    n.BackgroundColor3 = C.Panel
    n.BorderSizePixel = 0
    n.Parent = pg
    Instance.new("UICorner", n).CornerRadius = UDim.new(0, 10)
    local ns = Instance.new("UIStroke", n); ns.Color = C.Accent; ns.Thickness = 1.5
    local bar = Instance.new("Frame", n)
    bar.Size = UDim2.new(0, 4, 1, 0); bar.BackgroundColor3 = C.Accent; bar.BorderSizePixel = 0
    Instance.new("UICorner", bar).CornerRadius = UDim.new(0, 10)
    local t = Instance.new("TextLabel", n)
    t.Size = UDim2.new(1, -20, 0, 24); t.Position = UDim2.new(0, 15, 0, 6)
    t.BackgroundTransparency = 1; t.Text = title; t.TextColor3 = C.Accent
    t.TextSize = 14; t.Font = Enum.Font.GothamBold; t.TextXAlignment = Enum.TextXAlignment.Left
    local d = Instance.new("TextLabel", n)
    d.Size = UDim2.new(1, -20, 0, 24); d.Position = UDim2.new(0, 15, 0, 30)
    d.BackgroundTransparency = 1; d.Text = text; d.TextColor3 = C.Text
    d.TextSize = 12; d.Font = Enum.Font.Gotham; d.TextXAlignment = Enum.TextXAlignment.Left
    TweenService:Create(n, TweenInfo.new(0.4, Enum.EasingStyle.Quad), { Position = UDim2.new(0, 20, 0, 20) }):Play()
    task.delay(duration, function()
        TweenService:Create(n, TweenInfo.new(0.4), { Position = UDim2.new(0, -350, 0, 20) }):Play()
        task.wait(0.5); n:Destroy()
    end)
end

-- ═══════════════════════════════════════════
-- UI PRINCIPAL
-- ═══════════════════════════════════════════
pcall(function()
    if CoreGui:FindFirstChild("RollOnHub") then CoreGui.RollOnHub:Destroy() end
end)

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "RollOnHub"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.IgnoreGuiInset = true
pcall(function() ScreenGui.Parent = CoreGui end)
if not ScreenGui.Parent then ScreenGui.Parent = LP:WaitForChild("PlayerGui") end

local UIScale = Instance.new("UIScale")
UIScale.Scale = 1
UIScale.Parent = ScreenGui

-- Main
local Main = Instance.new("Frame")
Main.Size = UDim2.new(0, 600, 0, 440)
Main.Position = UDim2.new(0.5, -300, 0.5, -220)
Main.BackgroundColor3 = C.BG
Main.BorderSizePixel = 0
Main.Parent = ScreenGui
Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 14)

local mStroke = Instance.new("UIStroke", Main)
mStroke.Color = C.Accent; mStroke.Thickness = 1.5; mStroke.Transparency = 0.4

-- TopBar
local TopBar = Instance.new("Frame", Main)
TopBar.Size = UDim2.new(1, 0, 0, 45)
TopBar.BackgroundColor3 = C.Panel
TopBar.BorderSizePixel = 0
Instance.new("UICorner", TopBar).CornerRadius = UDim.new(0, 14)

local topCover = Instance.new("Frame", TopBar)
topCover.Size = UDim2.new(1, 0, 0, 14); topCover.Position = UDim2.new(0, 0, 1, -14)
topCover.BackgroundColor3 = C.Panel; topCover.BorderSizePixel = 0

local Logo = Instance.new("TextLabel", TopBar)
Logo.Size = UDim2.new(0, 45, 1, 0); Logo.Position = UDim2.new(0, 10, 0, 0)
Logo.BackgroundTransparency = 1; Logo.Text = "🧴"; Logo.TextSize = 24

local Title = Instance.new("TextLabel", TopBar)
Title.Size = UDim2.new(0, 200, 1, 0); Title.Position = UDim2.new(0, 55, 0, 0)
Title.BackgroundTransparency = 1; Title.Text = "ROLL-ON HUB"
Title.TextColor3 = C.Text; Title.TextSize = 15; Title.Font = Enum.Font.GothamBold
Title.TextXAlignment = Enum.TextXAlignment.Left

local SubTitle = Instance.new("TextLabel", TopBar)
SubTitle.Size = UDim2.new(0, 200, 1, 0); SubTitle.Position = UDim2.new(0, 165, 0, 0)
SubTitle.BackgroundTransparency = 1; SubTitle.Text = "  •  Blox Fruits"
SubTitle.TextColor3 = C.Accent2; SubTitle.TextSize = 12; SubTitle.Font = Enum.Font.Gotham
SubTitle.TextXAlignment = Enum.TextXAlignment.Left

local StatusDot = Instance.new("Frame", TopBar)
StatusDot.Size = UDim2.new(0, 8, 0, 8); StatusDot.Position = UDim2.new(1, -130, 0.5, -4)
StatusDot.BackgroundColor3 = C.Green; StatusDot.BorderSizePixel = 0
Instance.new("UICorner", StatusDot).CornerRadius = UDim.new(1, 0)

-- Botão -
local SizeDownBtn = Instance.new("TextButton", TopBar)
SizeDownBtn.Size = UDim2.new(0, 28, 0, 28); SizeDownBtn.Position = UDim2.new(1, -125, 0, 8)
SizeDownBtn.BackgroundColor3 = C.PanelAlt; SizeDownBtn.Text = "−"
SizeDownBtn.TextColor3 = C.Text; SizeDownBtn.TextSize = 16
SizeDownBtn.Font = Enum.Font.GothamBold; SizeDownBtn.BorderSizePixel = 0
Instance.new("UICorner", SizeDownBtn).CornerRadius = UDim.new(0, 8)

-- Botão +
local SizeUpBtn = Instance.new("TextButton", TopBar)
SizeUpBtn.Size = UDim2.new(0, 28, 0, 28); SizeUpBtn.Position = UDim2.new(1, -95, 0, 8)
SizeUpBtn.BackgroundColor3 = C.PanelAlt; SizeUpBtn.Text = "+"
SizeUpBtn.TextColor3 = C.Text; SizeUpBtn.TextSize = 16
SizeUpBtn.Font = Enum.Font.GothamBold; SizeUpBtn.BorderSizePixel = 0
Instance.new("UICorner", SizeUpBtn).CornerRadius = UDim.new(0, 8)

-- Minimizar
local MinBtn = Instance.new("TextButton", TopBar)
MinBtn.Size = UDim2.new(0, 30, 0, 30); MinBtn.Position = UDim2.new(1, -63, 0, 7)
MinBtn.BackgroundColor3 = C.PanelAlt; MinBtn.Text = "—"
MinBtn.TextColor3 = C.Text; MinBtn.TextSize = 16
MinBtn.Font = Enum.Font.GothamBold; MinBtn.BorderSizePixel = 0
Instance.new("UICorner", MinBtn).CornerRadius = UDim.new(0, 8)

-- Fechar
local CloseBtn = Instance.new("TextButton", TopBar)
CloseBtn.Size = UDim2.new(0, 30, 0, 30); CloseBtn.Position = UDim2.new(1, -32, 0, 7)
CloseBtn.BackgroundColor3 = C.Red; CloseBtn.Text = "✕"
CloseBtn.TextColor3 = C.Text; CloseBtn.TextSize = 14
CloseBtn.Font = Enum.Font.GothamBold; CloseBtn.BorderSizePixel = 0
Instance.new("UICorner", CloseBtn).CornerRadius = UDim.new(0, 8)

SizeUpBtn.MouseButton1Click:Connect(function()
    BF.UIScale = math.min(BF.UIScale + 0.1, 1.6)
    TweenService:Create(UIScale, TweenInfo.new(0.2), { Scale = BF.UIScale }):Play()
end)
SizeDownBtn.MouseButton1Click:Connect(function()
    BF.UIScale = math.max(BF.UIScale - 0.1, 0.6)
    TweenService:Create(UIScale, TweenInfo.new(0.2), { Scale = BF.UIScale }):Play()
end)

-- Drag
local dragging, dragInput, dragStart, startPos
TopBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true; dragStart = input.Position; startPos = Main.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then dragging = false end
        end)
    end
end)
TopBar.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
        dragInput = input
    end
end)
UserInputService.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        local delta = input.Position - dragStart
        Main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

-- Sidebar
local SideBar = Instance.new("Frame", Main)
SideBar.Size = UDim2.new(0, 140, 1, -45); SideBar.Position = UDim2.new(0, 0, 0, 45)
SideBar.BackgroundColor3 = C.Panel; SideBar.BorderSizePixel = 0

local sbCover = Instance.new("Frame", SideBar)
sbCover.Size = UDim2.new(0, 14, 1, 0); sbCover.Position = UDim2.new(1, -14, 0, 0)
sbCover.BackgroundColor3 = C.Panel; sbCover.BorderSizePixel = 0

local sideList = Instance.new("UIListLayout", SideBar)
sideList.Padding = UDim.new(0, 6); sideList.HorizontalAlignment = Enum.HorizontalAlignment.Center
local sidePad = Instance.new("UIPadding", SideBar); sidePad.PaddingTop = UDim.new(0, 12)

local Content = Instance.new("Frame", Main)
Content.Size = UDim2.new(1, -150, 1, -55); Content.Position = UDim2.new(0, 145, 0, 50)
Content.BackgroundTransparency = 1

-- Tabs
local Tabs = {}
local function CreateTab(name, icon)
    local Tab = Instance.new("TextButton")
    Tab.Size = UDim2.new(0, 122, 0, 34); Tab.BackgroundColor3 = C.BG
    Tab.Text = ""; Tab.BorderSizePixel = 0; Tab.Parent = SideBar
    Instance.new("UICorner", Tab).CornerRadius = UDim.new(0, 8)
    local st = Instance.new("UIStroke", Tab); st.Color = C.Accent; st.Transparency = 1; st.Thickness = 1.5
    local ic = Instance.new("TextLabel", Tab)
    ic.Size = UDim2.new(0, 25, 1, 0); ic.Position = UDim2.new(0, 8, 0, 0)
    ic.BackgroundTransparency = 1; ic.Text = icon; ic.TextSize = 15; ic.TextColor3 = C.Text
    local lb = Instance.new("TextLabel", Tab)
    lb.Size = UDim2.new(1, -38, 1, 0); lb.Position = UDim2.new(0, 33, 0, 0)
    lb.BackgroundTransparency = 1; lb.Text = name; lb.TextColor3 = C.TextDim
    lb.TextSize = 12; lb.Font = Enum.Font.GothamMedium; lb.TextXAlignment = Enum.TextXAlignment.Left
    local Page = Instance.new("ScrollingFrame")
    Page.Size = UDim2.new(1, 0, 1, 0); Page.BackgroundTransparency = 1
    Page.BorderSizePixel = 0; Page.ScrollBarThickness = 4
    Page.ScrollBarImageColor3 = C.Accent; Page.CanvasSize = UDim2.new(0, 0, 0, 0)
    Page.AutomaticCanvasSize = Enum.AutomaticSize.Y; Page.Visible = false
    Page.Parent = Content
    local pl = Instance.new("UIListLayout", Page); pl.Padding = UDim.new(0, 6)
    local pp = Instance.new("UIPadding", Page); pp.PaddingRight = UDim.new(0, 6); pp.PaddingBottom = UDim.new(0, 10)
    Tabs[name] = { Tab = Tab, Page = Page, Stroke = st, Label = lb }
    Tab.MouseButton1Click:Connect(function()
        for _, d in pairs(Tabs) do
            d.Page.Visible = false; d.Tab.BackgroundColor3 = C.BG
            d.Label.TextColor3 = C.TextDim; d.Stroke.Transparency = 1
        end
        Page.Visible = true; Tab.BackgroundColor3 = C.PanelAlt
        lb.TextColor3 = C.Text; st.Transparency = 0
    end)
    return Page
end

-- Componentes
local function Section(parent, text)
    local s = Instance.new("TextLabel")
    s.Size = UDim2.new(1, -5, 0, 26); s.BackgroundTransparency = 1
    s.Text = "▎ " .. text; s.TextColor3 = C.Accent2
    s.TextSize = 13; s.Font = Enum.Font.GothamBold
    s.TextXAlignment = Enum.TextXAlignment.Left; s.Parent = parent
end

local function Toggle(parent, text, default, callback)
    local f = Instance.new("Frame")
    f.Size = UDim2.new(1, -5, 0, 38); f.BackgroundColor3 = C.Panel
    f.BorderSizePixel = 0; f.Parent = parent
    Instance.new("UICorner", f).CornerRadius = UDim.new(0, 8)
    local st = Instance.new("UIStroke", f); st.Color = C.Accent; st.Transparency = 0.85
    local l = Instance.new("TextLabel", f)
    l.Size = UDim2.new(1, -70, 1, 0); l.Position = UDim2.new(0, 14, 0, 0)
    l.BackgroundTransparency = 1; l.Text = text; l.TextColor3 = C.Text
    l.TextSize = 13; l.Font = Enum.Font.Gotham; l.TextXAlignment = Enum.TextXAlignment.Left
    local b = Instance.new("TextButton", f)
    b.Size = UDim2.new(0, 42, 0, 20); b.Position = UDim2.new(1, -52, 0.5, -10)
    b.BackgroundColor3 = default and C.Accent or Color3.fromRGB(45, 45, 60)
    b.Text = ""; b.BorderSizePixel = 0
    Instance.new("UICorner", b).CornerRadius = UDim.new(1, 0)
    local cir = Instance.new("Frame", b)
    cir.Size = UDim2.new(0, 16, 0, 16)
    cir.Position = default and UDim2.new(1, -18, 0.5, -8) or UDim2.new(0, 2, 0.5, -8)
    cir.BackgroundColor3 = C.Text; cir.BorderSizePixel = 0
    Instance.new("UICorner", cir).CornerRadius = UDim.new(1, 0)
    local state = default
    b.MouseButton1Click:Connect(function()
        state = not state
        TweenService:Create(b, TweenInfo.new(0.2), {
            BackgroundColor3 = state and C.Accent or Color3.fromRGB(45, 45, 60)
        }):Play()
        TweenService:Create(cir, TweenInfo.new(0.2), {
            Position = state and UDim2.new(1, -18, 0.5, -8) or UDim2.new(0, 2, 0.5, -8)
        }):Play()
        if callback then callback(state) end
    end)
end

local function Slider(parent, text, min, max, default, callback)
    local f = Instance.new("Frame")
    f.Size = UDim2.new(1, -5, 0, 52); f.BackgroundColor3 = C.Panel
    f.BorderSizePixel = 0; f.Parent = parent
    Instance.new("UICorner", f).CornerRadius = UDim.new(0, 8)
    local st = Instance.new("UIStroke", f); st.Color = C.Accent; st.Transparency = 0.85
    local l = Instance.new("TextLabel", f)
    l.Size = UDim2.new(1, -90, 0, 18); l.Position = UDim2.new(0, 14, 0, 5)
    l.BackgroundTransparency = 1; l.Text = text; l.TextColor3 = C.Text
    l.TextSize = 13; l.Font = Enum.Font.Gotham; l.TextXAlignment = Enum.TextXAlignment.Left
    local vl = Instance.new("TextLabel", f)
    vl.Size = UDim2.new(0, 70, 0, 18); vl.Position = UDim2.new(1, -80, 0, 5)
    vl.BackgroundTransparency = 1; vl.Text = tostring(default); vl.TextColor3 = C.Accent2
    vl.TextSize = 13; vl.Font = Enum.Font.GothamBold; vl.TextXAlignment = Enum.TextXAlignment.Right
    local barBg = Instance.new("Frame", f)
    barBg.Size = UDim2.new(1, -28, 0, 5); barBg.Position = UDim2.new(0, 14, 0, 36)
    barBg.BackgroundColor3 = Color3.fromRGB(45, 45, 60); barBg.BorderSizePixel = 0
    Instance.new("UICorner", barBg).CornerRadius = UDim.new(1, 0)
    local fill = Instance.new("Frame", barBg)
    fill.Size = UDim2.new((default - min) / (max - min), 0, 1, 0)
    fill.BackgroundColor3 = C.Accent; fill.BorderSizePixel = 0
    Instance.new("UICorner", fill).CornerRadius = UDim.new(1, 0)
    local cir = Instance.new("Frame", barBg)
    cir.Size = UDim2.new(0, 14, 0, 14)
    cir.Position = UDim2.new((default - min) / (max - min), -7, 0.5, -7)
    cir.BackgroundColor3 = C.Text; cir.BorderSizePixel = 0
    Instance.new("UICorner", cir).CornerRadius = UDim.new(1, 0)
    local dragging = false
    local function update(input)
        local pos = math.clamp((input.Position.X - barBg.AbsolutePosition.X) / barBg.AbsoluteSize.X, 0, 1)
        local val = math.floor(min + (max - min) * pos)
        fill.Size = UDim2.new(pos, 0, 1, 0); cir.Position = UDim2.new(pos, -7, 0.5, -7)
        vl.Text = tostring(val)
        if callback then callback(val) end
    end
    barBg.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true; update(input)
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
            update(input)
        end
    end)
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)
end

local function Button(parent, text, callback, color)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(1, -5, 0, 34); b.BackgroundColor3 = color or C.Accent
    b.Text = text; b.TextColor3 = C.Text; b.TextSize = 13
    b.Font = Enum.Font.GothamBold; b.BorderSizePixel = 0; b.Parent = parent
    Instance.new("UICorner", b).CornerRadius = UDim.new(0, 8)
    b.MouseEnter:Connect(function()
        TweenService:Create(b, TweenInfo.new(0.15), { BackgroundColor3 = C.Accent2 }):Play()
    end)
    b.MouseLeave:Connect(function()
        TweenService:Create(b, TweenInfo.new(0.15), { BackgroundColor3 = color or C.Accent }):Play()
    end)
    b.MouseButton1Click:Connect(callback)
end

local function Dropdown(parent, text, options, default, callback)
    local f = Instance.new("Frame")
    f.Size = UDim2.new(1, -5, 0, 38); f.BackgroundColor3 = C.Panel
    f.BorderSizePixel = 0; f.Parent = parent
    Instance.new("UICorner", f).CornerRadius = UDim.new(0, 8)
    local st = Instance.new("UIStroke", f); st.Color = C.Accent; st.Transparency = 0.85
    local l = Instance.new("TextLabel", f)
    l.Size = UDim2.new(1, -120, 1, 0); l.Position = UDim2.new(0, 14, 0, 0)
    l.BackgroundTransparency = 1; l.Text = text; l.TextColor3 = C.Text
    l.TextSize = 13; l.Font = Enum.Font.Gotham; l.TextXAlignment = Enum.TextXAlignment.Left
    local sel = Instance.new("TextButton", f)
    sel.Size = UDim2.new(0, 100, 0, 24); sel.Position = UDim2.new(1, -110, 0.5, -12)
    sel.BackgroundColor3 = C.PanelAlt; sel.Text = default; sel.TextColor3 = C.Accent2
    sel.TextSize = 12; sel.Font = Enum.Font.GothamMedium; sel.BorderSizePixel = 0
    Instance.new("UICorner", sel).CornerRadius = UDim.new(0, 6)
    local open = false; local list
    sel.MouseButton1Click:Connect(function()
        open = not open
        if open then
            list = Instance.new("Frame", f)
            list.Size = UDim2.new(0, 100, 0, #options * 26)
            list.Position = UDim2.new(1, -110, 1, 4)
            list.BackgroundColor3 = C.Panel; list.BorderSizePixel = 0
            list.ZIndex = 5
            Instance.new("UICorner", list).CornerRadius = UDim.new(0, 6)
            local ll = Instance.new("UIListLayout", list); l
