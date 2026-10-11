local Players          = game:GetService("Players")
local RunService       = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService     = game:GetService("TweenService")
local CoreGui          = game:GetService("CoreGui")
local LocalPlayer      = Players.LocalPlayer
local Camera           = workspace.CurrentCamera

local IsMobile = UserInputService.TouchEnabled and not UserInputService.MouseEnabled

local C = {
    Pri = Color3.fromRGB(0, 170, 255),
    Acc = Color3.fromRGB(120, 220, 255),
    Cyan = Color3.fromRGB(0, 245, 255),
    Ice = Color3.fromRGB(200, 235, 255),
    Bg = Color3.fromRGB(12, 14, 22),
    BgSoft = Color3.fromRGB(20, 24, 36),
    Card = Color3.fromRGB(30, 36, 52),
    CardHi = Color3.fromRGB(42, 50, 70),
    Txt = Color3.fromRGB(245, 248, 255),
    TxtDim = Color3.fromRGB(155, 170, 200),
    Ok = Color3.fromRGB(0, 235, 140),
    Bad = Color3.fromRGB(255, 80, 100),
    Warn = Color3.fromRGB(255, 195, 70),
    Gold = Color3.fromRGB(255, 215, 80),
    Red = Color3.fromRGB(255, 60, 60),
    Blue = Color3.fromRGB(60, 160, 255),
    Green = Color3.fromRGB(60, 230, 130),
    Purple = Color3.fromRGB(180, 100, 255),
    White = Color3.fromRGB(255, 255, 255),
}

local S = {
    Anim = true, Speed = false, SpeedVal = 16, Jump = false, JumpVal = 120,
    Fly = false, FlySpeed = 60, Noclip = false, ESP = false, ESP_Role = true,
    XRay = false, XRay_Depth = 0.35, AutoGun = false, KillAura = false,
    KillAuraRange = 15, Fling = false, GodMode = false,
    AntiAfk = true, Fullbright = false, FOV = false, FOVVal = 90,
    AntiFling = true, AutoKillMurderer = false,
    VoidKill = false, VoidRange = 15, AutoVoid = false,
    AutoPickupGun = false, AutoPickupRange = 500,
    Aimbot = false, AimbotRange = 300, AimbotSmooth = 0.3, AimbotTeam = "Murderer",
    AutoShoot = false,
}

pcall(function()
    local o = CoreGui:FindFirstChild("EliteMM2Combat")
    if o then o:Destroy() end
end)

local function tw(o, t, p, st)
    local x = TweenService:Create(o, TweenInfo.new(S.Anim and t or 0, st or Enum.EasingStyle.Quart), p)
    x:Play()
    return x
end

local function cr(p, r)
    local c = Instance.new("UICorner")
    c.CornerRadius = typeof(r) == "number" and UDim.new(0, r) or r
    c.Parent = p
    return c
end

local function sk(p, col, th, tr)
    local s = Instance.new("UIStroke")
    s.Color = col or C.Pri
    s.Thickness = th or 1
    s.Transparency = tr or 0.3
    s.Parent = p
    return s
end

-- Detecção de role
local Roles = {}
local function detectRole(player)
    if player.Team then
        local t = player.Team.Name:lower()
        if t:find("murder") then return "Murderer" end
        if t:find("sheriff") then return "Sheriff" end
    end
    local ch = player.Character
    if ch then
        for _, o in ipairs(ch:GetChildren()) do
            if o:IsA("Tool") then
                local n = o.Name:lower()
                if n:find("knife") then return "Murderer" end
                if n:find("gun") or n:find("revolver") then return "Sheriff" end
            end
        end
    end
    local bp = player:FindFirstChild("Backpack")
    if bp then
        for _, o in ipairs(bp:GetChildren()) do
            if o:IsA("Tool") then
                local n = o.Name:lower()
                if n:find("knife") then return "Murderer" end
                if n:find("gun") or n:find("revolver") then return "Sheriff" end
            end
        end
    end
    return "Innocent"
end

local function roleColor(r)
    if r == "Murderer" then return C.Red end
    if r == "Sheriff" then return C.Blue end
    return C.Green
end

local function roleName(r)
    if r == "Murderer" then return "🔪 Assassino" end
    if r == "Sheriff" then return "🔫 Xerife" end
    return "😇 Inocente"
end

task.spawn(function()
    while true do
        pcall(function()
            for _, p in ipairs(Players:GetPlayers()) do
                Roles[p] = detectRole(p)
            end
        end)
        task.wait(1)
    end
end)

-- GUI
local GUI = Instance.new("ScreenGui")
GUI.Name = "EliteMM2Combat"
GUI.ResetOnSpawn = false
GUI.IgnoreGuiInset = true
GUI.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
GUI.DisplayOrder = 999
GUI.Parent = CoreGui

-- FAB
local FabFrame = Instance.new("Frame")
FabFrame.Size = UDim2.new(0, 56, 0, 56)
FabFrame.Position = UDim2.new(0, 16, 0.5, -28)
FabFrame.BackgroundColor3 = C.Bg
FabFrame.BorderSizePixel = 0
FabFrame.Visible = false
FabFrame.Active = true
FabFrame.Parent = GUI
cr(FabFrame, 14)
sk(FabFrame, C.Cyan, 1.5, 0.2)

local FabGlow = Instance.new("ImageLabel")
FabGlow.Size = UDim2.new(1, 20, 1, 20)
FabGlow.Position = UDim2.new(0, -10, 0, -10)
FabGlow.BackgroundTransparency = 1
FabGlow.Image = "rbxassetid://5028857084"
FabGlow.ImageColor3 = C.Cyan
FabGlow.ImageTransparency = 0.5
FabGlow.ZIndex = 0
FabGlow.Parent = FabFrame

local FabLogo = Instance.new("TextLabel")
FabLogo.Size = UDim2.new(1, 0, 1, 0)
FabLogo.BackgroundTransparency = 1
FabLogo.Text = "❄"
FabLogo.TextColor3 = C.Ice
FabLogo.TextSize = 28
FabLogo.Font = Enum.Font.GothamBlack
FabLogo.Parent = FabFrame

local Fab = Instance.new("TextButton")
Fab.Size = UDim2.new(1, 0, 1, 0)
Fab.BackgroundTransparency = 1
Fab.Text = ""
Fab.ZIndex = 10
Fab.Parent = FabFrame

-- Drag FAB
local fabDrag, fabDS, fabSP, fabMoved = false, nil, nil, false
Fab.InputBegan:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
        fabDrag, fabDS, fabSP, fabMoved = true, i.Position, FabFrame.Position, false
    end
end)
UserInputService.InputChanged:Connect(function(i)
    if fabDrag and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
        local d = i.Position - fabDS
        if math.abs(d.X) > 5 or math.abs(d.Y) > 5 then fabMoved = true end
        FabFrame.Position = UDim2.new(0, fabSP.X.Offset + d.X, 0, fabSP.Y.Offset + d.Y)
    end
end)
UserInputService.InputEnded:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
        fabDrag = false
    end
end)

-- Pulsar FAB
task.spawn(function()
    while FabFrame.Parent do
        pcall(function()
            if FabFrame.Visible and S.Anim then
                tw(FabGlow, 1.5, { ImageTransparency = 0.8 }, Enum.EasingStyle.Sine)
                task.wait(1.5)
                tw(FabGlow, 1.5, { ImageTransparency = 0.4 }, Enum.EasingStyle.Sine)
                task.wait(1.5)
            else task.wait(0.5) end
        end)
    end
end)

-- PAINEL
local Panel = Instance.new("Frame")
Panel.AnchorPoint = Vector2.new(0.5, 0.5)
Panel.Position = UDim2.new(0.5, 0, 0.5, 0)
Panel.Size = UDim2.new(0, 310, 0, 400)
Panel.BackgroundColor3 = C.Bg
Panel.BackgroundTransparency = 0.05
Panel.BorderSizePixel = 0
Panel.Active = true
Panel.ClipsDescendants = true
Panel.ZIndex = 2
Panel.Parent = GUI
cr(Panel, 10)
sk(Panel, C.Cyan, 1.5, 0.15)

local pg = Instance.new("UIGradient")
pg.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, C.BgSoft),
    ColorSequenceKeypoint.new(0.5, C.Bg),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(6, 8, 14)),
})
pg.Rotation = 135
pg.Parent = Panel

-- TOPBAR
local TopBar = Instance.new("Frame")
TopBar.Size = UDim2.new(1, 0, 0, 44)
TopBar.BackgroundColor3 = C.BgSoft
TopBar.BackgroundTransparency = 0.1
TopBar.BorderSizePixel = 0
TopBar.ZIndex = 3
TopBar.Active = true
TopBar.Parent = Panel
cr(TopBar, 10)

local tg = Instance.new("UIGradient")
tg.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.fromRGB(0, 70, 150)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(0, 20, 50)),
})
tg.Parent = TopBar

local tf = Instance.new("Frame")
tf.Size = UDim2.new(1, 0, 0, 10)
tf.Position = UDim2.new(0, 0, 1, -10)
tf.BackgroundColor3 = C.BgSoft
tf.BackgroundTransparency = 0.1
tf.BorderSizePixel = 0
tf.ZIndex = TopBar.ZIndex - 1
tf.Parent = TopBar

local TopLogo = Instance.new("TextLabel")
TopLogo.Size = UDim2.new(0, 32, 0, 32)
TopLogo.Position = UDim2.new(0, 8, 0.5, -16)
TopLogo.BackgroundColor3 = C.Bg
TopLogo.Text = "❄"
TopLogo.TextColor3 = C.Ice
TopLogo.TextSize = 18
TopLogo.Font = Enum.Font.GothamBlack
TopLogo.BorderSizePixel = 0
TopLogo.ZIndex = 4
TopLogo.Parent = TopBar
cr(TopLogo, 8)
sk(TopLogo, C.Cyan, 1.5, 0.1)

local tTxt = Instance.new("TextLabel")
tTxt.Size = UDim2.new(1, -130, 0, 18)
tTxt.Position = UDim2.new(0, 46, 0, 6)
tTxt.BackgroundTransparency = 1
tTxt.Text = "ELITE MM2"
tTxt.TextColor3 = C.White
tTxt.TextSize = 12
tTxt.Font = Enum.Font.GothamBold
tTxt.TextXAlignment = Enum.TextXAlignment.Left
tTxt.ZIndex = 4
tTxt.Parent = TopBar

local myRoleLbl = Instance.new("TextLabel")
myRoleLbl.Size = UDim2.new(1, -130, 0, 14)
myRoleLbl.Position = UDim2.new(0, 46, 0, 24)
myRoleLbl.BackgroundTransparency = 1
myRoleLbl.Text = "Detectando..."
myRoleLbl.TextColor3 = C.Cyan
myRoleLbl.TextSize = 9
myRoleLbl.Font = Enum.Font.GothamBold
myRoleLbl.TextXAlignment = Enum.TextXAlignment.Left
myRoleLbl.ZIndex = 4
myRoleLbl.Parent = TopBar

task.spawn(function()
    while myRoleLbl.Parent do
        pcall(function()
            local r = Roles[LocalPlayer] or detectRole(LocalPlayer)
            myRoleLbl.Text = roleName(r)
            myRoleLbl.TextColor3 = roleColor(r)
        end)
        task.wait(1)
    end
end)

local function ctrl(icon, x, isClose)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(0, 24, 0, 24)
    b.Position = UDim2.new(1, x, 0.5, -12)
    b.BackgroundColor3 = C.Bg
    b.BackgroundTransparency = 0.4
    b.Text = icon
    b.TextColor3 = C.Txt
    b.TextSize = 12
    b.Font = Enum.Font.GothamBold
    b.BorderSizePixel = 0
    b.AutoButtonColor = false
    b.ZIndex = 5
    b.Parent = TopBar
    cr(b, 6)
    sk(b, isClose and C.Bad or C.Pri, 1, 0.5)

    b.MouseEnter:Connect(function()
        tw(b, 0.15, { BackgroundTransparency = 0.1, BackgroundColor3 = isClose and C.Bad or C.Pri })
    end)
    b.MouseLeave:Connect(function()
        tw(b, 0.15, { BackgroundTransparency = 0.4, BackgroundColor3 = C.Bg })
    end)
    return b
end

local minB = ctrl("—", -84)
local resB = ctrl("⟳", -58)
local clB  = ctrl("✕", -32, true)

-- Drag painel
local drag, ds, sp = false, nil, nil
TopBar.InputBegan:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
        drag, ds, sp = true, i.Position, Panel.Position
    end
end)
UserInputService.InputChanged:Connect(function(i)
    if drag and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
        local d = i.Position - ds
        Panel.Position = UDim2.new(0.5, sp.X.Offset + d.X, 0.5, sp.Y.Offset + d.Y)
    end
end)
UserInputService.InputEnded:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
        drag = false
    end
end)

-- TAB BAR
local TBWrap = Instance.new("Frame")
TBWrap.Size = UDim2.new(1, -16, 0, 32)
TBWrap.Position = UDim2.new(0, 8, 0, 52)
TBWrap.BackgroundColor3 = C.Bg
TBWrap.BackgroundTransparency = 0.4
TBWrap.BorderSizePixel = 0
TBWrap.ZIndex = 3
TBWrap.ClipsDescendants = true
TBWrap.Parent = Panel
cr(TBWrap, 6)
sk(TBWrap, C.Pri, 1, 0.5)

local TBScroll = Instance.new("ScrollingFrame")
TBScroll.Size = UDim2.new(1, 0, 1, 0)
TBScroll.BackgroundTransparency = 1
TBScroll.BorderSizePixel = 0
TBScroll.ScrollBarThickness = 0
TBScroll.ScrollingDirection = Enum.ScrollingDirection.X
TBScroll.CanvasSize = UDim2.new(0, 0, 0, 0)
TBScroll.Parent = TBWrap

local TBLayout = Instance.new("UIListLayout")
TBLayout.FillDirection = Enum.FillDirection.Horizontal
TBLayout.Padding = UDim.new(0, 3)
TBLayout.SortOrder = Enum.SortOrder.LayoutOrder
TBLayout.Parent = TBScroll

local TBPad = Instance.new("UIPadding")
TBPad.PaddingLeft = UDim.new(0, 4)
TBPad.PaddingTop = UDim.new(0, 4)
TBPad.Parent = TBScroll

TBLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    TBScroll.CanvasSize = UDim2.new(0, TBLayout.AbsoluteContentSize.X + 10, 0, 0)
end)

local Content = Instance.new("Frame")
Content.Size = UDim2.new(1, -16, 1, -96)
Content.Position = UDim2.new(0, 8, 0, 90)
Content.BackgroundTransparency = 1
Content.ClipsDescendants = true
Content.Parent = Panel

local Tabs, Active = {}, nil
local orders = {}

local function newTab(id, label, icon)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(0, 68, 0, 24)
    b.BackgroundColor3 = C.Bg
    b.BackgroundTransparency = 1
    b.Text = icon .. " " .. label
    b.TextColor3 = C.TxtDim
    b.TextSize = IsMobile and 9 or 10
    b.Font = Enum.Font.GothamSemibold
    b.BorderSizePixel = 0
    b.LayoutOrder = #orders + 1
    b.Parent = TBScroll
    cr(b, 5)
    table.insert(orders, id)

    local p = Instance.new("ScrollingFrame")
    p.Size = UDim2.new(1, 0, 1, 0)
    p.BackgroundTransparency = 1
    p.BorderSizePixel = 0
    p.ScrollBarThickness = 3
    p.ScrollBarImageColor3 = C.Pri
    p.CanvasSize = UDim2.new(0, 0, 0, 0)
    p.Visible = false
    p.Parent = Content

    local l = Instance.new("UIListLayout")
    l.Padding = UDim.new(0, 6)
    l.SortOrder = Enum.SortOrder.LayoutOrder
    l.Parent = p

    local pad = Instance.new("UIPadding")
    pad.PaddingTop = UDim.new(0, 2)
    pad.PaddingBottom = UDim.new(0, 8)
    pad.PaddingLeft = UDim.new(0, 2)
    pad.PaddingRight = UDim.new(0, 4)
    pad.Parent = p

    l:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
        p.CanvasSize = UDim2.new(0, 0, 0, l.AbsoluteContentSize.Y + 12)
    end)

    Tabs[id] = { B = b, P = p }

    b.MouseButton1Click:Connect(function()
        if Active == id then return end
        if Active and Tabs[Active] then
            Tabs[Active].B.TextColor3 = C.TxtDim
            Tabs[Active].B.BackgroundTransparency = 1
            Tabs[Active].B.BackgroundColor3 = C.Bg
            Tabs[Active].P.Visible = false
        end
        p.Visible = true
        b.TextColor3 = C.White
        b.BackgroundTransparency = 0.1
        b.BackgroundColor3 = C.Pri
        Active = id
    end)

    return p
end

-- Componentes
local function section(parent, titulo)
    local f = Instance.new("Frame")
    f.Size = UDim2.new(1, 0, 0, 22)
    f.BackgroundTransparency = 1
    f.Parent = parent
    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, 0, 0, 16)
    lbl.BackgroundTransparency = 1
    lbl.Text = titulo
    lbl.TextColor3 = C.Ice
    lbl.TextSize = 11
    lbl.Font = Enum.Font.GothamBold
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Parent = f
    local ln = Instance.new("Frame")
    ln.Size = UDim2.new(1, -80, 0, 1)
    ln.Position = UDim2.new(0, 0, 0, 18)
    ln.BackgroundColor3 = C.Pri
    ln.BackgroundTransparency = 0.5
    ln.BorderSizePixel = 0
    ln.Parent = f
end

local function btn(parent, txt, icon, cb, col)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(1, 0, 0, IsMobile and 32 or 30)
    b.BackgroundColor3 = C.Card
    b.BackgroundTransparency = 0.15
    b.Text = ""
    b.BorderSizePixel = 0
    b.Parent = parent
    cr(b, 5)
    sk(b, col or C.Pri, 1, 0.55)

    local i = Instance.new("TextLabel")
    i.Size = UDim2.new(0, 22, 1, 0)
    i.Position = UDim2.new(0, 6, 0, 0)
    i.BackgroundTransparency = 1
    i.Text = icon or "›"
    i.TextColor3 = col or C.Pri
    i.TextSize = 13
    i.Font = Enum.Font.GothamBold
    i.Parent = b

    local l = Instance.new("TextLabel")
    l.Size = UDim2.new(1, -36, 1, 0)
    l.Position = UDim2.new(0, 30, 0, 0)
    l.BackgroundTransparency = 1
    l.Text = txt
    l.TextColor3 = C.Txt
    l.TextSize = 11
    l.Font = Enum.Font.GothamSemibold
    l.TextXAlignment = Enum.TextXAlignment.Left
    l.Parent = b

    b.MouseEnter:Connect(function()
        tw(b, 0.15, { BackgroundTransparency = 0, BackgroundColor3 = C.CardHi })
    end)
    b.MouseLeave:Connect(function()
        tw(b, 0.15, { BackgroundTransparency = 0.15, BackgroundColor3 = C.Card })
    end)
    b.MouseButton1Click:Connect(function()
        if cb then pcall(cb) end
    end)
    return b
end

local function tgl(parent, txt, def, cb)
    local st = def or false
    local f = Instance.new("Frame")
    f.Size = UDim2.new(1, 0, 0, IsMobile and 32 or 30)
    f.BackgroundColor3 = C.Card
    f.BackgroundTransparency = 0.15
    f.BorderSizePixel = 0
    f.Parent = parent
    cr(f, 5)
    sk(f, C.Pri, 1, 0.6)

    local l = Instance.new("TextLabel")
    l.Size = UDim2.new(1, -60, 1, 0)
    l.Position = UDim2.new(0, 10, 0, 0)
    l.BackgroundTransparency = 1
    l.Text = txt
    l.TextColor3 = C.Txt
    l.TextSize = IsMobile and 10 or 11
    l.Font = Enum.Font.Gotham
    l.TextXAlignment = Enum.TextXAlignment.Left
    l.Parent = f

    local tk = Instance.new("TextButton")
    tk.Size = UDim2.new(0, 38, 0, 18)
    tk.Position = UDim2.new(1, -46, 0.5, -9)
    tk.BackgroundColor3 = st and C.Pri or Color3.fromRGB(45, 52, 70)
    tk.Text = ""
    tk.BorderSizePixel = 0
    tk.Parent = f
    cr(tk, UDim.new(1, 0))

    local kn = Instance.new("Frame")
    kn.Size = UDim2.new(0, 14, 0, 14)
    kn.Position = st and UDim2.new(1, -16, 0.5, -7) or UDim2.new(0, 2, 0.5, -7)
    kn.BackgroundColor3 = C.White
    kn.BorderSizePixel = 0
    kn.Parent = tk
    cr(kn, UDim.new(1, 0))

    tk.MouseButton1Click:Connect(function()
        st = not st
        tw(kn, 0.2, { Position = st and UDim2.new(1, -16, 0.5, -7) or UDim2.new(0, 2, 0.5, -7) })
        tw(tk, 0.2, { BackgroundColor3 = st and C.Pri or Color3.fromRGB(45, 52, 70) })
        if cb then pcall(cb, st) end
    end)
    return { Set = function(v) st = v end }
end

local function sld(parent, txt, mn, mx, df, cb)
    local v = df or mn
    local f = Instance.new("Frame")
    f.Size = UDim2.new(1, 0, 0, IsMobile and 46 or 44)
    f.BackgroundColor3 = C.Card
    f.BackgroundTransparency = 0.15
    f.BorderSizePixel = 0
    f.Parent = parent
    cr(f, 5)
    sk(f, C.Pri, 1, 0.6)

    local l = Instance.new("TextLabel")
    l.Size = UDim2.new(1, -60, 0, 16)
    l.Position = UDim2.new(0, 10, 0, 4)
    l.BackgroundTransparency = 1
    l.Text = txt
    l.TextColor3 = C.Txt
    l.TextSize = IsMobile and 10 or 11
    l.Font = Enum.Font.Gotham
    l.TextXAlignment = Enum.TextXAlignment.Left
    l.Parent = f

    local vl = Instance.new("TextLabel")
    vl.Size = UDim2.new(0, 50, 0, 16)
    vl.Position = UDim2.new(1, -55, 0, 4)
    vl.BackgroundTransparency = 1
    vl.Text = tostring(v)
    vl.TextColor3 = C.Cyan
    vl.TextSize = 11
    vl.Font = Enum.Font.GothamBold
    vl.TextXAlignment = Enum.TextXAlignment.Right
    vl.Parent = f

    local br = Instance.new("TextButton")
    br.Size = UDim2.new(1, -20, 0, 6)
    br.Position = UDim2.new(0, 10, 1, -14)
    br.BackgroundColor3 = Color3.fromRGB(35, 42, 58)
    br.Text = ""
    br.BorderSizePixel = 0
    br.Parent = f
    cr(br, UDim.new(1, 0))

    local fl = Instance.new("Frame")
    fl.Size = UDim2.new((v - mn) / (mx - mn), 0, 1, 0)
    fl.BackgroundColor3 = C.Pri
    fl.BorderSizePixel = 0
    fl.Parent = br
    cr(fl, UDim.new(1, 0))

    local kn = Instance.new("Frame")
    kn.Size = UDim2.new(0, 12, 0, 12)
    kn.AnchorPoint = Vector2.new(0.5, 0.5)
    kn.Position = UDim2.new((v - mn) / (mx - mn), 0, 0.5, 0)
    kn.BackgroundColor3 = C.White
    kn.BorderSizePixel = 0
    kn.ZIndex = 2
    kn.Parent = br
    cr(kn, UDim.new(1, 0))

    local dr = false
    local function sv(nv)
        nv = math.clamp(nv, mn, mx)
        v = math.floor(nv * 100 + 0.5) / 100
        vl.Text = tostring(v)
        local a = (v - mn) / (mx - mn)
        fl.Size = UDim2.new(a, 0, 1, 0)
        kn.Position = UDim2.new(a, 0, 0.5, 0)
        if cb then pcall(cb, v) end
    end

    local function pi(i)
        local r = (i.Position.X - br.AbsolutePosition.X) / br.AbsoluteSize.X
        sv(mn + r * (mx - mn))
    end

    br.InputBegan:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
            dr = true
            pi(i)
        end
    end)
    br.InputChanged:Connect(function(i)
        if dr and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
            pi(i)
        end
    end)
    UserInputService.InputEnded:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
            dr = false
        end
    end)

    return { Set = sv }
end

-- HOME
local HomeP = newTab("Home", "Início", "🏠")
section(HomeP, "SEU PAPEL")
local myCard = Instance.new("Frame")
myCard.Size = UDim2.new(1, 0, 0, 52)
myCard.BackgroundColor3 = C.Card
myCard.BackgroundTransparency = 0.15
myCard.BorderSizePixel = 0
myCard.Parent = HomeP
cr(myCard, 5)
local mySt = sk(myCard, C.Cyan, 1.5, 0.3)

local myIc = Instance.new("TextLabel")
myIc.Size = UDim2.new(0, 34, 0, 34)
myIc.Position = UDim2.new(0, 8, 0.5, -17)
myIc.BackgroundTransparency = 1
myIc.Text = "❄"
myIc.TextSize = 22
myIc.Parent = myCard

local myT = Instance.new("TextLabel")
myT.Size = UDim2.new(1, -56, 0, 18)
myT.Position = UDim2.new(0, 46, 0, 10)
myT.BackgroundTransparency = 1
myT.Text = "Detectando..."
myT.TextColor3 = C.Cyan
myT.TextSize = 14
myT.Font = Enum.Font.GothamBold
myT.TextXAlignment = Enum.TextXAlignment.Left
myT.Parent = myCard

local myH = Instance.new("TextLabel")
myH.Size = UDim2.new(1, -56, 0, 12)
myH.Position = UDim2.new(0, 46, 0, 30)
myH.BackgroundTransparency = 1
myH.Text = "Aguardando..."
myH.TextColor3 = C.TxtDim
myH.TextSize = 9
myH.Font = Enum.Font.Gotham
myH.TextXAlignment = Enum.TextXAlignment.Left
myH.Parent = myCard

task.spawn(function()
    while myCard.Parent do
        pcall(function()
            local r = Roles[LocalPlayer] or detectRole(LocalPlayer)
            myT.Text = roleName(r)
            myT.TextColor3 = roleColor(r)
            mySt.Color = roleColor(r)
            myIc.Text = r == "Murderer" and "🔪" or r == "Sheriff" and "🔫" or "😇"
            myH.Text = r == "Murderer" and "Elimine todos" or r == "Sheriff" and "Proteja os inocentes" or "Sobreviva"
        end)
        task.wait(0.5)
    end
end)

section(HomeP, "AÇÕES")
btn(HomeP, "Reexecutar", "🔄", function()
    GUI:Destroy()
end)
btn(HomeP, "Destruir", "🗑️", function()
    GUI:Destroy()
end, C.Bad)

-- ============================================================
-- VOID (NOVA ABA)
-- ============================================================
local VoidP = newTab("Void", "Void", "🕳️")

section(VoidP, "VOID KILL", "Joga o assassinino no void do jogo")

tgl(VoidP, "Ativar Void Kill (Manual)", false, function(v)
    S.VoidKill = v
end)

sld(VoidP, "Alcance do Void", 5, 60, 15, function(v)
    S.VoidRange = v
end)

section(VoidP, "MODO AUTOMÁTICO")
tgl(VoidP, "Auto Void (Matar Murder)", false, function(v)
    S.AutoVoid = v
end)

section(VoidP, "AÇÕES")
btn(VoidP, "Mandar Murder para o Void", "💀", function()
    local target = nil
    for p, r in pairs(Roles) do
        if r == "Murderer" and p.Character then
            target = p.Character
            break
        end
    end
    if target then
        local hrp = target:FindFirstChild("HumanoidRootPart")
        if hrp then
            -- Envia para muito abaixo do mapa (killzone)
            hrp.CFrame = CFrame.new(hrp.Position.X, -500, hrp.Position.Z)
        end
    end
end, C.Red)

btn(VoidP, "Mandar Todos para o Void", "🌪️", function()
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character then
            local hrp = p.Character:FindFirstChild("HumanoidRootPart")
            if hrp then
                hrp.CFrame = CFrame.new(hrp.Position.X, -500, hrp.Position.Z)
            end
        end
    end
end, C.Bad)

btn(VoidP, "Mandar Xerife para o Void", "🔫", function()
    for p, r in pairs(Roles) do
        if r == "Sheriff" and p.Character then
            local hrp = p.Character:FindFirstChild("HumanoidRootPart")
            if hrp then
                hrp.CFrame = CFrame.new(hrp.Position.X, -500, hrp.Position.Z)
                break
            end
        end
    end
end, C.Blue)

-- Loop do Auto Void
task.spawn(function()
    while GUI.Parent do
        pcall(function()
            if S.AutoVoid then
                for p, r in pairs(Roles) do
                    if r == "Murderer" and p.Character then
                        local hum = p.Character:FindFirstChildOfClass("Humanoid")
                        if hum and hum.Health > 0 then
                            local hrp = p.Character:FindFirstChild("HumanoidRootPart")
                            if hrp then
                                hrp.CFrame = CFrame.new(hrp.Position.X, -500, hrp.Position.Z)
                            end
                        end
                    end
                end
            end
        end)
        task.wait(0.5)
    end
end)

-- ============================================================
-- AUTO PICKUP GUN (melhorado)
-- ============================================================
local function findGun()
    for _, o in ipairs(workspace:GetDescendants()) do
        if o:IsA("Tool") then
            local n = o.Name:lower()
            if n:find("gun") or n:find("revolver") or n:find("sheriff") then
                return o
            end
        end
    end
    -- Também procura em Backpacks
    for _, p in ipairs(Players:GetPlayers()) do
        local bp = p:FindFirstChild("Backpack")
        if bp then
            for _, o in ipairs(bp:GetChildren()) do
                if o:IsA("Tool") then
                    local n = o.Name:lower()
                    if n:find("gun") or n:find("revolver") then
                        return o
                    end
                end
            end
        end
    end
    return nil
end

local function teleportAndPickup()
    local g = findGun()
    if not g then return false end
    local ch = LocalPlayer.Character
    if not ch then return false end
    local hrp = ch:FindFirstChild("HumanoidRootPart")
    local hum = ch:FindFirstChildOfClass("Humanoid")
    if not hrp or not hum then return false end

    -- Se a arma for um Tool solto no chão, teleporta até ela
    if g:IsA("Tool") and g.Parent == workspace then
        local handle = g:FindFirstChild("Handle")
        if handle and handle:IsA("BasePart") then
            hrp.CFrame = CFrame.new(handle.Position + Vector3.new(0, 2, 0))
            task.wait(0.1)
        end
    end

    -- Equipa
    if g.Parent ~= ch then
        pcall(function() hum:EquipTool(g) end)
    end
    return true
end

task.spawn(function()
    while GUI.Parent do
        pcall(function()
            if S.AutoPickupGun then
                teleportAndPickup()
            end
        end)
        task.wait(0.5)
    end
end)

-- GUN TAB
local GunP = newTab("Gun", "Arma", "🔫")

section(GunP, "AUTO COLETAR ARMA DO XERIFE", "Teleporta até a arma quando cair")
tgl(GunP, "Auto Coletar Arma", false, function(v)
    S.AutoPickupGun = v
end)

sld(GunP, "Distância Máxima", 50, 2000, 500, function(v)
    S.AutoPickupRange = v
end)

section(GunP, "AÇÕES")
btn(GunP, "Procurar Arma", "🔍", function()
    local g = findGun()
    if g then
        -- Sinal visual
        local highlight = Instance.new("Highlight")
        highlight.FillColor = C.Gold
        highlight.OutlineColor = C.White
        highlight.Parent = g
        task.delay(2, function() highlight:Destroy() end)
    end
end)

btn(GunP, "Teleportar + Coletar", "🎯", function()
    teleportAndPickup()
end)

btn(GunP, "Equipar Se Tiver", "✋", function()
    local ch = LocalPlayer.Character
    local hum = ch and ch:FindFirstChildOfClass("Humanoid")
    if not hum then return end
    for _, t in ipairs(ch:GetChildren()) do
        if t:IsA("Tool") then
            pcall(function() hum:EquipTool(t) end)
            return
        end
    end
    for _, t in ipairs(LocalPlayer.Backpack:GetChildren()) do
        if t:IsA("Tool") then
            pcall(function() hum:EquipTool(t) end)
            return
        end
    end
end)

-- ============================================================
-- AIMBOT (NOVA ABA)
-- ============================================================
local AimP = newTab("Aim", "Aimbot", "🎯")

local aimConn

local function getTarget()
    local bestTarget = nil
    local bestDist = S.AimbotRange

    local myCh = LocalPlayer.Character
    if not myCh then return nil end
    local myHrp = myCh:FindFirstChild("HumanoidRootPart")
    if not myHrp then return nil end

    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character then
            local role = Roles[p] or "Innocent"
            local valid = (S.AimbotTeam == "Murderer" and role == "Murderer")
                       or (S.AimbotTeam == "Sheriff" and role == "Sheriff")
                       or (S.AimbotTeam == "All" and true)
            if valid then
                local hum = p.Character:FindFirstChildOfClass("Humanoid")
                local hrp = p.Character:FindFirstChild("HumanoidRootPart")
                if hum and hum.Health > 0 and hrp then
                    local dist = (myHrp.Position - hrp.Position).Magnitude
                    if dist < bestDist then
                        bestDist = dist
                        bestTarget = hrp
                    end
                end
            end
        end
    end
    return bestTarget
end

local function runAimbot()
    if aimConn then return end
    aimConn = RunService.RenderStepped:Connect(function()
        if not S.Aimbot then return end
        local target = getTarget()
        if target then
            local myCh = LocalPlayer.Character
            local myHrp = myCh and myCh:FindFirstChild("HumanoidRootPart")
            if myHrp then
                -- Mira a câmera no alvo (smooth)
                local dir = (target.Position - Camera.CFrame.Position).Unit
                local targetCF = CFrame.new(Camera.CFrame.Position, Camera.CFrame.Position + dir)
                Camera.CFrame = Camera.CFrame:Lerp(targetCF, 1 - S.AimbotSmooth)
            end
        end
    end)
end

section(AimP, "AIMBOT", "Auto mira no alvo selecionado")
tgl(AimP, "Ativar Aimbot", false, function(v)
    S.Aimbot = v
    if v then
        runAimbot()
    else
        if aimConn then aimConn:Disconnect() aimConn = nil end
    end
end)

section(AimP, "ALVO")
btn(AimP, "🎯 Assassino", "🔪", function()
    S.AimbotTeam = "Murderer"
end, C.Red)
btn(AimP, "🔫 Xerife", "🔫", function()
    S.AimbotTeam = "Sheriff"
end, C.Blue)
btn(AimP, "🌍 Todos", "🌐", function()
    S.AimbotTeam = "All"
end, C.Gold)

sld(AimP, "Alcance do Aimbot", 20, 500, 300, function(v)
    S.AimbotRange = v
end)

sld(AimP, "Suavidade (0=lento, 1=rápido)", 0, 0.99, 0.3, function(v)
    S.AimbotSmooth = v
end)

-- Auto Shoot (atirar automaticamente quando a arma do xerife estiver na mão)
section(AimP, "AUTO SHOOT", "Atira automaticamente no alvo")
tgl(AimP, "Ativar Auto Shoot", false, function(v)
    S.AutoShoot = v
end)

task.spawn(function()
    while GUI.Parent do
        pcall(function()
            if S.AutoShoot then
                local ch = LocalPlayer.Character
                if ch then
                    for _, t in ipairs(ch:GetChildren()) do
                        if t:IsA("Tool") then
                            local n = t.Name:lower()
                            if n:find("gun") or n:find("revolver") then
                                -- Tenta ativar a arma
                                if t:FindFirstChild("Fire") then
                                    t.Fire:FireServer()
                                end
                                local target = getTarget()
                                if target then
                                    pcall(function()
                                        t:Activate()
                                    end)
                                end
                            end
                        end
                    end
                end
            end
        end)
        task.wait(0.15)
    end
end)

section(AimP, "INFO")
local aimInfo = Instance.new("TextLabel")
aimInfo.Size = UDim2.new(1, 0, 0, 50)
aimInfo.BackgroundColor3 = C.Card
aimInfo.BackgroundTransparency = 0.15
aimInfo.Text = "Aimbot move a câmera pro alvo.\nAuto Shoot atira automaticamente\nse você estiver com a arma."
aimInfo.TextColor3 = C.TxtDim
aimInfo.TextSize = 9
aimInfo.Font = Enum.Font.Gotham
aimInfo.TextWrapped = true
aimInfo.BorderSizePixel = 0
aimInfo.Parent = AimP
cr(aimInfo, 5)
sk(aimInfo, C.Pri, 1, 0.5)

-- ============================================================
-- X-RAY
-- ============================================================
local XRayP = newTab("X-Ray", "X-Ray", "🔍")
local xrayConn
local xraySaved = {}

local function applyXRay()
    if not LocalPlayer.Character then return end
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj:IsA("BasePart") then
            local isChar = false
            for _, p in ipairs(Players:GetPlayers()) do
                if p.Character and obj:IsDescendantOf(p.Character) then
                    isChar = true
                    break
                end
            end
            if not isChar then
                if not xraySaved[obj] then
                    xraySaved[obj] = obj.LocalTransparencyModifier
                end
                obj.LocalTransparencyModifier = S.XRay_Depth
            end
        end
    end
end

local function removeXRay()
    for obj, v in pairs(xraySaved) do
        pcall(function() if obj and obj.Parent then obj.LocalTransparencyModifier = v end end)
    end
    xraySaved = {}
end

section(XRayP, "X-RAY")
tgl(XRayP, "Ativar X-Ray", false, function(v)
    S.XRay = v
    if v then
        if not xrayConn then
            xrayConn = RunService.Heartbeat:Connect(function()
                if S.XRay then applyXRay() end
            end)
        end
    else
        if xrayConn then xrayConn:Disconnect() xrayConn = nil end
        removeXRay()
    end
end)

sld(XRayP, "Transparência", 0, 0.9, 0.35, function(v)
    S.XRay_Depth = v
end)

section(XRayP, "PRESETS")
btn(XRayP, "Sutil", "🌫️", function() S.XRay_Depth = 0.6 end)
btn(XRayP, "Equilibrado", "⚖️", function() S.XRay_Depth = 0.35 end)
btn(XRayP, "Fantasma", "👻", function() S.XRay_Depth = 0.1 end)

-- ============================================================
-- PLAYER
-- ============================================================
local PlayP = newTab("Play", "Player", "🏃")
section(PlayP, "VELOCIDADE")
tgl(PlayP, "Ativar Velocidade", false, function(v)
    S.Speed = v
    local ch = LocalPlayer.Character
    if ch then
        local h = ch:FindFirstChildOfClass("Humanoid")
        if h then h.WalkSpeed = v and S.SpeedVal or 16 end
    end
end)
sld(PlayP, "Velocidade", 16, 500, 16, function(v)
    S.SpeedVal = v
    if S.Speed then
        local ch = LocalPlayer.Character
        if ch then
            local h = ch:FindFirstChildOfClass("Humanoid")
            if h then h.WalkSpeed = v end
        end
    end
end)

section(PlayP, "SALTO")
tgl(PlayP, "Salto Alto", false, function(v)
    S.Jump = v
    local ch = LocalPlayer.Character
    if ch then
        local h = ch:FindFirstChildOfClass("Humanoid")
        if h then h.JumpPower = v and S.JumpVal or 50 end
    end
end)
sld(PlayP, "Força do Salto", 50, 500, 120, function(v)
    S.JumpVal = v
end)

section(PlayP, "NOCLIP")
local nocConn
tgl(PlayP, "Atravessar Paredes", false, function(v)
    S.Noclip = v
    if v and not nocConn then
        nocConn = RunService.Stepped:Connect(function()
            if not S.Noclip then return end
            local ch = LocalPlayer.Character
            if not ch then return end
            for _, p in ipairs(ch:GetDescendants()) do
                if p:IsA("BasePart") then p.CanCollide = false end
            end
        end)
    end
end)

section(PlayP, "GOD MODE")
tgl(PlayP, "God Mode", false, function(v)
    local ch = LocalPlayer.Character
    if ch then
        local h = ch:FindFirstChildOfClass("Humanoid")
        if h then h.MaxHealth = v and math.huge or 100; h.Health = v and math.huge or 100 end
    end
end)

-- ============================================================
-- COMBAT
-- ============================================================
local CombP = newTab("Combat", "Combate", "⚔️")
local auraConn

section(CombP, "KILL AURA")
tgl(CombP, "Ativar", false, function(v)
    S.KillAura = v
    if v and not auraConn then
        auraConn = RunService.Heartbeat:Connect(function()
            if not S.KillAura then return end
            local ch = LocalPlayer.Character
            if not ch then return end
            local hrp = ch:FindFirstChild("HumanoidRootPart")
            if not hrp then return end
            for _, p in ipairs(Players:GetPlayers()) do
                if p ~= LocalPlayer and p.Character then
                    local tHRP = p.Character:FindFirstChild("HumanoidRootPart")
                    local tHum = p.Character:FindFirstChildOfClass("Humanoid")
                    if tHRP and tHum then
                        if (hrp.Position - tHRP.Position).Magnitude < S.KillAuraRange then
                            tHum.Health = 0
                        end
                    end
                end
            end
        end)
    end
end)
sld(CombP, "Alcance", 5, 60, 15, function(v) S.KillAuraRange = v end)

section(CombP, "AÇÕES")
btn(CombP, "Kill All", "💀", function()
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character then
            local h = p.Character:FindFirstChildOfClass("Humanoid")
            if h then h.Health = 0 end
        end
    end
end, C.Bad)

btn(CombP, "Matar Assassino", "🔪", function()
    for p, r in pairs(Roles) do
        if r == "Murderer" and p.Character then
            local h = p.Character:FindFirstChildOfClass("Humanoid")
            if h then h.Health = 0 end
        end
    end
end, C.Red)

-- ============================================================
-- FLY
-- ============================================================
local FlyP = newTab("Fly", "Voar", "✈️")
local flyConn

section(FlyP, "VOO")
tgl(FlyP, "Ativar Voo", false, function(v)
    S.Fly = v
    if v then
        local ch = LocalPlayer.Character
        if not ch then return end
        local hrp = ch:FindFirstChild("HumanoidRootPart")
        local hum = ch:FindFirstChildOfClass("Humanoid")
        if not hrp or not hum then return end
        hum.PlatformStand = true
        local bv = Instance.new("BodyVelocity")
        bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bv.Parent = hrp
        local bg = Instance.new("BodyGyro")
        bg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
        bg.P = 5000
        bg.Parent = hrp
        if flyConn then flyConn:Disconnect() end
        flyConn = RunService.RenderStepped:Connect(function()
            if not S.Fly then return end
            bg.CFrame = Camera.CFrame
            local mv = Vector3.new(0, 0, 0)
            local cc = Camera.CFrame
            if UserInputService:IsKeyDown(Enum.KeyCode.W) then mv = mv + cc.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.S) then mv = mv - cc.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.A) then mv = mv - cc.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.D) then mv = mv + cc.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.Space) then mv = mv + Vector3.new(0, 1, 0) end
            if IsMobile then
                local hm = hum.MoveDirection
                if hm.Magnitude > 0 then mv = cc.LookVector * hm.Z + cc.RightVector * hm.X end
            end
            bv.Velocity = mv.Magnitude > 0 and mv.Unit * S.FlySpeed or Vector3.new(0, 0, 0)
        end)
    else
        if flyConn then flyConn:Disconnect() flyConn = nil end
        local ch = LocalPlayer.Character
        if ch then
            local hrp = ch:FindFirstChild("HumanoidRootPart")
            local hum = ch:FindFirstChildOfClass("Humanoid")
            if hrp then
                for _, o in ipairs(hrp:GetChildren()) do
                    if o:IsA("BodyVelocity") or o:IsA("BodyGyro") then o:Destroy() end
                end
            end
            if hum then hum.PlatformStand = false end
        end
    end
end)
sld(FlyP, "Velocidade", 10, 500, 60, function(v) S.FlySpeed = v end)

-- ============================================================
-- ESP
-- ============================================================
local ESPP = newTab("ESP", "Visão", "👁️")
local espStore = {}
local espConn

local function createESP(p)
    if p == LocalPlayer or espStore[p] then return end
    local ch = p.Character
    if not ch or not ch:FindFirstChild("HumanoidRootPart") then return end
    local bb = Instance.new("BillboardGui")
    bb.Size = UDim2.new(0, 130, 0, 50)
    bb.StudsOffset = Vector3.new(0, 3.2, 0)
    bb.AlwaysOnTop = true
    bb.Parent = ch

    local fr = Instance.new("Frame")
    fr.Size = UDim2.new(1, 0, 1, 0)
    fr.BackgroundColor3 = C.Bg
    fr.BackgroundTransparency = 0.35
    fr.BorderSizePixel = 0
    fr.Parent = bb
    cr(fr, 4)
    local st = sk(fr, C.Pri, 1.5, 0.2)

    local rl = Instance.new("TextLabel")
    rl.Size = UDim2.new(1, -8, 0, 16)
    rl.Position = UDim2.new(0, 4, 0, 3)
    rl.BackgroundTransparency = 1
    rl.Text = "INOCENTE"
    rl.TextColor3 = C.Green
    rl.TextSize = 11
    rl.Font = Enum.Font.GothamBold
    rl.TextXAlignment = Enum.TextXAlignment.Left
    rl.Parent = fr

    local nl = Instance.new("TextLabel")
    nl.Size = UDim2.new(1, -8, 0, 14)
    nl.Position = UDim2.new(0, 4, 0, 19)
    nl.BackgroundTransparency = 1
    nl.Text = p.Name
    nl.TextColor3 = C.White
    nl.TextSize = 10
    nl.Font = Enum.Font.GothamSemibold
    nl.TextXAlignment = Enum.TextXAlignment.Left
    nl.Parent = fr

    local dl = Instance.new("TextLabel")
    dl.Size = UDim2.new(1, -8, 0, 12)
    dl.Position = UDim2.new(0, 4, 0, 34)
    dl.BackgroundTransparency = 1
    dl.Text = "0m"
    dl.TextColor3 = C.Cyan
    dl.TextSize = 9
    dl.Font = Enum.Font.GothamBold
    dl.TextXAlignment = Enum.TextXAlignment.Left
    dl.Parent = fr

    espStore[p] = { BB = bb, DL = dl, ST = st, RL = rl }
end

for _, p in ipairs(Players:GetPlayers()) do createESP(p) end
Players.PlayerAdded:Connect(function(p)
    p.CharacterAdded:Connect(function()
        task.wait(0.4)
        createESP(p)
    end)
end)
Players.PlayerRemoving:Connect(function(p)
    if espStore[p] then
        pcall(function() espStore[p].BB:Destroy() end)
        espStore[p] = nil
    end
end)

section(ESPP, "ESP")
tgl(ESPP, "Ativar ESP", false, function(v)
    S.ESP = v
    if v and not espConn then
        espConn = RunService.RenderStepped:Connect(function()
            if not S.ESP then return end
            for p, d in pairs(espStore) do
                if not p.Character then
                    pcall(function() d.BB:Destroy() end)
                    espStore[p] = nil
                else
                    d.BB.Enabled = true
                    local hrp = p.Character:FindFirstChild("HumanoidRootPart")
                    if hrp then
                        local dist = (Camera.CFrame.Position - hrp.Position).Magnitude
                        d.DL.Text = string.format("%.0fm", dist)
                        local r = Roles[p] or "Innocent"
                        d.RL.Text = roleName(r)
                        d.RL.TextColor3 = roleColor(r)
                        d.ST.Color = roleColor(r)
                    end
                end
            end
        end)
    end
    if not v then
        for _, d in pairs(espStore) do d.BB.Enabled = false end
    end
end)

btn(ESPP, "Recarregar Lista", "🔄", function()
    for _, p in ipairs(Players:GetPlayers()) do
        if espStore[p] then pcall(function() espStore[p].BB:Destroy() end) espStore[p] = nil end
        createESP(p)
    end
end)

-- ============================================================
-- WORLD
-- ============================================================
local WorldP = newTab("World", "Mundo", "🌍")
section(WorldP, "ILUMINAÇÃO")
tgl(WorldP, "Fullbright", false, function(v)
    if v then
        game:GetService("Lighting").Brightness = 3
        game:GetService("Lighting").ClockTime = 12
    else
        game:GetService("Lighting").Brightness = 1
        game:GetService("Lighting").ClockTime = 14
    end
end)

section(WorldP, "PROTEÇÕES")
tgl(WorldP, "Anti-AFK", true, function(v) S.AntiAfk = v end)

task.spawn(function()
    while GUI.Parent do
        pcall(function()
            if S.AntiAfk then
                local vim = game:GetService("VirtualUser")
                vim:CaptureController()
                vim:ClickButton2(Vector2.new())
            end
        end)
        task.wait(30)
    end
end)

-- ============================================================
-- CONTROLES DE JANELA
-- ============================================================
local function hidePanel()
    tw(Panel, 0.25, { Size = UDim2.new(0, 0, 0, 0), BackgroundTransparency = 1 }, Enum.EasingStyle.Back)
    task.wait(0.26)
    Panel.Visible = false
    FabFrame.Visible = true
    FabFrame.Size = UDim2.new(0, 0, 0, 0)
    tw(FabFrame, 0.35, { Size = UDim2.new(0, 56, 0, 56) }, Enum.EasingStyle.Back)
end

minB.MouseButton1Click:Connect(hidePanel)
clB.MouseButton1Click:Connect(hidePanel)

resB.MouseButton1Click:Connect(function()
    tw(Panel, 0.35, {
        Size = UDim2.new(0, 310, 0, 400),
        Position = UDim2.new(0.5, 0, 0.5, 0)
    }, Enum.EasingStyle.Back)
end)

Fab.MouseButton1Click:Connect(function()
    if fabMoved then fabMoved = false return end
    Panel.Visible = true
    Panel.Position = UDim2.new(0.5, 0, 0.5, 0)
    Panel.Size = UDim2.new(0, 0, 0, 0)
    Panel.BackgroundTransparency = 0.05
    FabFrame.Visible = false
    tw(Panel, 0.35, { Size = UDim2.new(0, 310, 0, 400) }, Enum.EasingStyle.Back)
end)

Tabs["Home"].P.Visible = true
Tabs["Home"].B.TextColor3 = C.White
Tabs["Home"].B.BackgroundTransparency = 0.1
Tabs["Home"].B.BackgroundColor3 = C.Pri
Active = "Home"

Panel.Visible = true
task.spawn(function()
    task.wait(0.05)
    Panel.Size = UDim2.new(0, 0, 0, 0)
    tw(Panel, 0.4, { Size = UDim2.new(0, 310, 0, 400) }, Enum.EasingStyle.Back)
end)

print("[Elite MM2 Combat] Carregado!")

GUI.Destroying:Connect(function()
    if espConn then espConn:Disconnect() end
    if flyConn then flyConn:Disconnect() end
    if nocConn then nocConn:Disconnect() end
    if auraConn then auraConn:Disconnect() end
    if aimConn then aimConn:Disconnect() end
    if xrayConn then xrayConn:Disconnect() end
    for _, d in pairs(espStore) do pcall(function() d.BB:Destroy() end) end
    removeXRay()
end)
