local IUIS = game:GetService("UserInputService")
local ITS  = game:GetService("TweenService")
local IPS  = game:GetService("Players")
local RS   = game:GetService("RunService")
local WS   = game:GetService("Workspace")
local ILP  = IPS.LocalPlayer
local HttpService = game:GetService("HttpService")

local function INew(Class, Props)
    local I = Instance.new(Class)
    for k, v in pairs(Props or {}) do I[k] = v end
    return I
end
local function ICorner(P, R) return INew("UICorner", { CornerRadius = UDim.new(0, R or 6), Parent = P }) end
local function IStroke(P, C, T, Tr)
    return INew("UIStroke", {
        Color = C or Color3.fromRGB(45, 45, 55),
        Thickness = T or 1, Transparency = Tr or 0,
        ApplyStrokeMode = Enum.ApplyStrokeMode.Border, Parent = P,
    })
end
local function IPad(P, L, R, T, B)
    return INew("UIPadding", {
        PaddingLeft = UDim.new(0, L or 0), PaddingRight = UDim.new(0, R or 0),
        PaddingTop = UDim.new(0, T or 0), PaddingBottom = UDim.new(0, B or 0),
        Parent = P,
    })
end
local function IGradient(P, C1, C2, Rot)
    return INew("UIGradient", {
        Color = ColorSequence.new({
            ColorSequenceKeypoint.new(0, C1),
            ColorSequenceKeypoint.new(1, C2),
        }),
        Rotation = Rot or 0, Parent = P,
    })
end
local function IShadowFixed(P, Transparency)
    return INew("ImageLabel", {
        Name = "Shadow", BackgroundTransparency = 1,
        Position = UDim2.new(0, -15, 0, -15),
        Size = UDim2.new(1, 30, 1, 30),
        Image = "rbxassetid://6014261993", ImageColor3 = Color3.new(0, 0, 0),
        ImageTransparency = Transparency or 0.55,
        ScaleType = Enum.ScaleType.Slice, SliceCenter = Rect.new(49, 49, 450, 450),
        ZIndex = -1, Parent = P,
    })
end

local function IHiddenContainer()
    local ok, hui = pcall(function()
        if gethui then return gethui() end
        if getgenv and getgenv().gethui then return getgenv().gethui() end
        return nil
    end)
    if ok and hui then return hui end
    local ok2, cg = pcall(function() return game:GetService("CoreGui") end)
    if ok2 and cg then return cg end
    return ILP:WaitForChild("PlayerGui")
end

local Library = {
    Toggles = {}, Options = {}, Flags = {},
    Window = nil, ScreenGui = nil, Unloaded = false,
    Connections = {}, Threads = {},
    OpenKey = Enum.KeyCode.RightShift,
    Scheme = {
        FontColor        = Color3.fromRGB(245, 245, 245),
        MainColor        = Color3.fromRGB(12, 12, 14),
        AccentColor      = Color3.fromRGB(255, 255, 255),
        BackgroundColor  = Color3.fromRGB(22, 22, 26),
        OutlineColor     = Color3.fromRGB(40, 40, 48),
        SidebarColor     = Color3.fromRGB(8, 8, 10),
        HoverColor       = Color3.fromRGB(36, 36, 42),
        TabColor         = Color3.fromRGB(22, 22, 26),
        TextMuted        = Color3.fromRGB(140, 140, 150),
    },
}

local function AddConn(conn)
    table.insert(Library.Connections, conn)
    return conn
end

local function IMakeDraggable(Frame, Handle)
    Handle = Handle or Frame
    local dragging, dragStart, startPos
    AddConn(Handle.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true; dragStart = input.Position; startPos = Frame.Position
            AddConn(input.Changed:Connect(function() if input.UserInputState == Enum.UserInputState.End then dragging = false end end))
        end
    end))
    AddConn(IUIS.InputChanged:Connect(function(input)
        if not dragging or Library.Unloaded then return end
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            local d = input.Position - dragStart
            Frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset+d.X, startPos.Y.Scale, startPos.Y.Offset+d.Y)
        end
    end))
end

local IWindow = { Tabs={}, TabButtons={}, ActiveTab=nil }

function Library:CreateWindow(opts)
    opts = opts or {}
    local Title = opts.Title or "PoniLOL"
    local Subtitle = "Discord/Peaged"

    local SG = INew("ScreenGui", { Name = "PoniLOL_"..math.random(1,1e9), ResetOnSpawn = false, IgnoreGuiInset = true, ZIndexBehavior = Enum.ZIndexBehavior.Sibling, DisplayOrder = 999, Parent = IHiddenContainer() })
    Library.ScreenGui = SG

    local GuiVisible = true
    AddConn(IUIS.InputBegan:Connect(function(Input, GP)
        if GP or Library.Unloaded then return end
        if Input.UserInputType == Enum.UserInputType.Keyboard then
            if Input.KeyCode == Library.OpenKey then
                GuiVisible = not GuiVisible
                SG.Enabled = GuiVisible
            end
        end
    end))

    local vp = WS.CurrentCamera and WS.CurrentCamera.ViewportSize or Vector2.new(1920,1080)
    local scale = math.clamp(math.min(vp.X/1920, vp.Y/1080), 0.55, 1)
    local WinW = math.floor(720*scale); local WinH = math.floor(500*scale); local SideW = math.floor(200*scale)

    local Root = INew("Frame", { Name = "Root", AnchorPoint = Vector2.new(0.5,0), Position = UDim2.new(0.5,0,0,12), Size = UDim2.new(0,WinW,0,WinH), BackgroundTransparency = 1, Parent = SG })
    local Main = INew("Frame", { Name = "Window", Size = UDim2.new(1,0,1,0), BackgroundColor3 = Library.Scheme.MainColor, BorderSizePixel = 0, Parent = Root })
    ICorner(Main, 12); IStroke(Main, Color3.fromRGB(255,255,255), 1, 0.85)
    IGradient(Main, Color3.fromRGB(20,20,24), Color3.fromRGB(8,8,10), 90); IShadowFixed(Main, 0.55)

    local DragBar = INew("Frame", { Name = "DragBar", Size = UDim2.new(1,0,0,32), Position = UDim2.new(0,0,0,0), BackgroundTransparency = 1, BorderSizePixel = 0, ZIndex = 5, Parent = Main })
    INew("TextLabel", { Size = UDim2.new(0,100,1,0), Position = UDim2.new(1,-110,0,0), BackgroundTransparency = 1, Font = Enum.Font.GothamBold, TextSize = 10, TextColor3 = Library.Scheme.TextMuted, TextXAlignment = Enum.TextXAlignment.Right, Text = "PoniLOL", ZIndex = 6, Parent = DragBar })
    IMakeDraggable(Root, DragBar)

    local Sidebar = INew("Frame", { Name = "Sidebar", Size = UDim2.new(0,SideW,1,0), BackgroundColor3 = Library.Scheme.SidebarColor, BorderSizePixel = 0, ClipsDescendants = true, Parent = Main })
    ICorner(Sidebar, 12)
    IGradient(Sidebar, Color3.fromRGB(16,16,18), Color3.fromRGB(0,0,0), 90)

    local BrandRow = INew("Frame", { Size = UDim2.new(1,-24,0,44), Position = UDim2.new(0,12,0,16), BackgroundTransparency = 1, Parent = Sidebar })
    local LogoBox = INew("Frame", { Size = UDim2.new(0,32,0,32), Position = UDim2.new(0,0,0.5,-16), BackgroundColor3 = Library.Scheme.AccentColor, BorderSizePixel = 0, Parent = BrandRow })
    ICorner(LogoBox, 8); IGradient(LogoBox, Color3.fromRGB(255,255,255), Color3.fromRGB(80,80,80), 45)
    INew("TextLabel", { Size = UDim2.new(1,0,1,0), BackgroundTransparency = 1, Font = Enum.Font.GothamBlack, TextSize = 16, TextColor3 = Color3.fromRGB(0,0,0), Text = "P", Parent = LogoBox })
    INew("TextLabel", { Size = UDim2.new(1,-40,0,16), Position = UDim2.new(0,40,0,2), BackgroundTransparency = 1, Font = Enum.Font.GothamBold, TextSize = 13, TextColor3 = Library.Scheme.FontColor, TextXAlignment = Enum.TextXAlignment.Left, Text = Title, Parent = BrandRow })
    INew("TextLabel", { Size = UDim2.new(1,-40,0,12), Position = UDim2.new(0,40,0,18), BackgroundTransparency = 1, Font = Enum.Font.Gotham, TextSize = 10, TextColor3 = Library.Scheme.TextMuted, TextXAlignment = Enum.TextXAlignment.Left, Text = Subtitle, Parent = BrandRow })

    local TabList = INew("ScrollingFrame", { Size = UDim2.new(1,-16,1,-170), Position = UDim2.new(0,8,0,72), BackgroundTransparency = 1, BorderSizePixel = 0, ScrollBarThickness = 0, CanvasSize = UDim2.new(0,0,0,0), AutomaticCanvasSize = Enum.AutomaticSize.Y, Parent = Sidebar })
    INew("UIListLayout", { SortOrder = Enum.SortOrder.LayoutOrder, Padding = UDim.new(0,6), Parent = TabList })
    IPad(TabList, 0, 0, 0, 0)

    local UserBadge = INew("Frame", { Size = UDim2.new(1,-24,0,50), Position = UDim2.new(0,12,1,-62), AnchorPoint = Vector2.new(0,0), BackgroundColor3 = Library.Scheme.TabColor, BorderSizePixel = 0, Parent = Sidebar })
    ICorner(UserBadge, 8); IStroke(UserBadge, Library.Scheme.OutlineColor, 1, 0.5); IGradient(UserBadge, Color3.fromRGB(24,24,28), Color3.fromRGB(12,12,14), 90)
    local BadgeAv = INew("ImageLabel", { Size = UDim2.new(0,34,0,34), Position = UDim2.new(0,8,0.5,-17), BackgroundColor3 = Library.Scheme.HoverColor, BorderSizePixel = 0, Image = "", Parent = UserBadge })
    ICorner(BadgeAv, 17); IStroke(BadgeAv, Color3.fromRGB(200,200,200), 1.2, 0.2)
    INew("TextLabel", { Size = UDim2.new(1,-56,0,14), Position = UDim2.new(0,50,0,10), BackgroundTransparency = 1, Font = Enum.Font.GothamBold, TextSize = 11, TextColor3 = Library.Scheme.FontColor, TextXAlignment = Enum.TextXAlignment.Left, TextTruncate = Enum.TextTruncate.AtEnd, Text = ILP.DisplayName, Parent = UserBadge })
    INew("TextLabel", { Size = UDim2.new(1,-56,0,12), Position = UDim2.new(0,50,1,-22), BackgroundTransparency = 1, Font = Enum.Font.Gotham, TextSize = 10, TextColor3 = Library.Scheme.TextMuted, TextXAlignment = Enum.TextXAlignment.Left, TextTruncate = Enum.TextTruncate.AtEnd, Text = "@"..ILP.Name, Parent = UserBadge })
    task.spawn(function()
        local ok, url = pcall(function() return IPS:GetUserThumbnailAsync(ILP.UserId, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size150x150) end)
        if ok and url and not Library.Unloaded then BadgeAv.Image = url end
    end)

    local Content = INew("Frame", { Name = "Content", Size = UDim2.new(1,-(SideW+10),1,-24), Position = UDim2.new(0, SideW+2, 0, 12), BackgroundTransparency = 1, Parent = Main })
    local Topbar = INew("Frame", { Name = "Topbar", Size = UDim2.new(1,0,0,32), BackgroundTransparency = 1, Parent = Content })
    local PageTitle = INew("TextLabel", { Size = UDim2.new(1,0,1,0), BackgroundTransparency = 1, Font = Enum.Font.GothamBold, TextSize = 14, TextColor3 = Library.Scheme.FontColor, TextXAlignment = Enum.TextXAlignment.Left, Text = "", Parent = Topbar })
    local TabHolder = INew("ScrollingFrame", { Name = "TabHolder", Size = UDim2.new(1,0,1,-42), Position = UDim2.new(0,0,0,42), BackgroundTransparency = 1, BorderSizePixel = 0, ScrollBarThickness = 0, CanvasSize = UDim2.new(0,0,0,0), AutomaticCanvasSize = Enum.AutomaticSize.Y, ScrollingDirection = Enum.ScrollingDirection.Y, ClipsDescendants = true, Parent = Content })

    local Window = { Tabs = {} }

    local function MakeColumn(Parent, OffsetX, WidthRatio)
        return INew("Frame", { Size = UDim2.new(WidthRatio, -5, 0, 0), Position = UDim2.new(OffsetX, 0, 0, 0), BackgroundTransparency = 1, AutomaticSize = Enum.AutomaticSize.Y, Parent = Parent })
    end

    function Window:AddTab(TabName, Icon)
        local Tab = { Name = TabName, Container = nil }
        local TB = INew("TextButton", { Name = "Tab_"..TabName, Size = UDim2.new(1,0,0,34), BackgroundColor3 = Library.Scheme.HoverColor, BackgroundTransparency = 1, BorderSizePixel = 0, Font = Enum.Font.GothamMedium, TextSize = 12, TextColor3 = Library.Scheme.TextMuted, TextXAlignment = Enum.TextXAlignment.Left, Text = "    "..(Icon and (tostring(Icon).."   ") or "")..TabName, AutoButtonColor = false, Parent = TabList })
        ICorner(TB, 6)
        local Ind = INew("Frame", { Size = UDim2.new(0,3,0,0), Position = UDim2.new(0,0,0.5,0), AnchorPoint = Vector2.new(0,0.5), BackgroundColor3 = Library.Scheme.AccentColor, BorderSizePixel = 0, Parent = TB })
        ICorner(Ind, 2); IGradient(Ind, Color3.fromRGB(255,255,255), Color3.fromRGB(110,110,110), 90)

        local Page = INew("Frame", { Name = "Page_"..TabName, Size = UDim2.new(1,0,0,0), BackgroundTransparency = 1, Visible = false, AutomaticSize = Enum.AutomaticSize.Y, Parent = TabHolder })
        Tab.Container = Page

        local function Select()
            for _, o in pairs(IWindow.TabButtons) do
                o.Button.BackgroundTransparency = 1; o.Button.TextColor3 = Library.Scheme.TextMuted
                if o.Indicator then ITS:Create(o.Indicator, TweenInfo.new(0.2), { Size = UDim2.new(0,3,0,0) }):Play() end
            end
            ITS:Create(TB, TweenInfo.new(0.2), { BackgroundTransparency = 0.2, BackgroundColor3 = Library.Scheme.HoverColor }):Play()
            TB.TextColor3 = Library.Scheme.FontColor
            ITS:Create(Ind, TweenInfo.new(0.25, Enum.EasingStyle.Quad), { Size = UDim2.new(0,3,0,18) }):Play()
            for _, o in pairs(IWindow.Tabs) do o.Container.Visible = false end
            Page.Visible = true; PageTitle.Text = TabName; IWindow.ActiveTab = Tab
            TabHolder.CanvasPosition = Vector2.new(0, 0)
        end

        AddConn(TB.MouseButton1Click:Connect(Select))
        AddConn(TB.MouseEnter:Connect(function() if IWindow.ActiveTab ~= Tab then ITS:Create(TB, TweenInfo.new(0.15), { BackgroundTransparency = 0.75, BackgroundColor3 = Library.Scheme.HoverColor }):Play(); TB.TextColor3 = Library.Scheme.FontColor end end))
        AddConn(TB.MouseLeave:Connect(function() if IWindow.ActiveTab ~= Tab then ITS:Create(TB, TweenInfo.new(0.15), { BackgroundTransparency = 1 }):Play(); TB.TextColor3 = Library.Scheme.TextMuted end end))

        table.insert(IWindow.TabButtons, { Button=TB, Tab=Tab, Indicator=Ind })
        table.insert(IWindow.Tabs, Tab)

        if not IWindow.ActiveTab then Select() end

        local L = MakeColumn(Page, 0, 0.5); local R = MakeColumn(Page, 0.5, 0.5)
        INew("UIListLayout", { SortOrder = Enum.SortOrder.LayoutOrder, Padding = UDim.new(0,10), Parent = L })
        INew("UIListLayout", { SortOrder = Enum.SortOrder.LayoutOrder, Padding = UDim.new(0,10), Parent = R })
        IPad(L, 0, 0, 0, 10); IPad(R, 0, 0, 0, 10)

        local function syncPage() Page.Size = UDim2.new(1, 0, 0, math.max(L.AbsoluteSize.Y, R.AbsoluteSize.Y)) end
        AddConn(L:GetPropertyChangedSignal("AbsoluteSize"):Connect(syncPage)); AddConn(R:GetPropertyChangedSignal("AbsoluteSize"):Connect(syncPage))

        local GO = 0
        local function MakeGroupbox(Name, Column, Order)
            GO += 1
            local Box = INew("Frame", { Name = "G_"..Name, Size = UDim2.new(1,0,0,32), BackgroundColor3 = Library.Scheme.TabColor, BorderSizePixel = 0, AutomaticSize = Enum.AutomaticSize.Y, LayoutOrder = Order or GO, Parent = Column })
            ICorner(Box, 8); IStroke(Box, Library.Scheme.OutlineColor, 1, 0.4); IGradient(Box, Color3.fromRGB(26,26,30), Color3.fromRGB(14,14,16), 90)
            local Head = INew("TextButton", { Size = UDim2.new(1,0,0,32), BackgroundTransparency = 1, Font = Enum.Font.GothamBold, TextSize = 12, TextColor3 = Library.Scheme.FontColor, TextXAlignment = Enum.TextXAlignment.Left, Text = "   "..Name.."            ▲", Parent = Box, AutoButtonColor = false })
            local HLine = INew("Frame", { Size = UDim2.new(1,-16,0,1), Position = UDim2.new(0,8,0,32), BackgroundColor3 = Library.Scheme.OutlineColor, BorderSizePixel = 0, Parent = Box })
            IGradient(HLine, Color3.fromRGB(70,70,80), Color3.fromRGB(25,25,30), 0)
            local Body = INew("Frame", { Size = UDim2.new(1,-16,0,0), Position = UDim2.new(0,8,0,38), BackgroundTransparency = 1, AutomaticSize = Enum.AutomaticSize.Y, Parent = Box })
            IPad(Box, 0, 0, 0, 12); INew("UIListLayout", { SortOrder = Enum.SortOrder.LayoutOrder, Padding = UDim.new(0,6), Parent = Body })
            local Collapsed = false
            AddConn(Head.MouseButton1Click:Connect(function() Collapsed = not Collapsed; Body.Visible = not Collapsed; HLine.Visible = not Collapsed; Head.Text = "   "..Name..(Collapsed and "            ▼" or "            ▲") end))

            local G = { Container=Box, Body=Body, Elements={}, _order=0 }
            local function Next() G._order += 1; return G._order end
            local function BaseRow(H) return INew("Frame", { Size = UDim2.new(1,0,0,H), BackgroundTransparency = 1, LayoutOrder = Next(), Parent = Body }) end
            local function Reg(flag, obj) if not flag then return end; Library.Options[flag] = obj; Library.Flags[flag] = obj end

            function G:AddDivider()
                local Row = INew("Frame", { Size = UDim2.new(1,0,0,1), BackgroundTransparency = 1, LayoutOrder = Next(), Parent = Body })
                local Ln = INew("Frame", { Size = UDim2.new(1,0,0,1), Position = UDim2.new(0,0,0.5,0), BackgroundColor3 = Library.Scheme.OutlineColor, BorderSizePixel = 0, Parent = Row })
                IGradient(Ln, Color3.fromRGB(60,60,70), Color3.fromRGB(25,25,30), 0)
            end

            function G:AddLabel(Text, RichText)
                local Row = BaseRow(20)
                return INew("TextLabel", { Size = UDim2.new(1,0,1,0), BackgroundTransparency = 1, Font = Enum.Font.Gotham, TextSize = 11, TextColor3 = Library.Scheme.TextMuted, TextXAlignment = Enum.TextXAlignment.Left, TextWrapped = true, RichText = RichText and true or false, Text = tostring(Text), Parent = Row })
            end

            function G:AddToggleBox(Flag, Opt)
                Opt = Opt or {}; local Row = BaseRow(26); local State = Opt.Default or false
                local T = { Value = State, Type = "Toggle", Flag = Flag, Callbacks = {} }
                local Box = INew("TextButton", { Size = UDim2.new(0,18,0,18), Position = UDim2.new(0,0,0.5,-9), BackgroundColor3 = State and Library.Scheme.AccentColor or Library.Scheme.BackgroundColor, BorderSizePixel = 0, Text = "", AutoButtonColor = false, Parent = Row })
                ICorner(Box, 5); local S = IStroke(Box, State and Library.Scheme.AccentColor or Library.Scheme.OutlineColor, 1.5, 0)
                local Lbl = INew("TextLabel", { Size = UDim2.new(1,-24,1,0), Position = UDim2.new(0,24,0,0), BackgroundTransparency = 1, Font = Enum.Font.Gotham, TextSize = 11, TextColor3 = Library.Scheme.FontColor, TextXAlignment = Enum.TextXAlignment.Left, Text = tostring(Opt.Text or Flag), Parent = Row })
                local function Set(v, silent)
                    State = v; T.Value = v
                    if v then ITS:Create(Box, TweenInfo.new(0.2), { BackgroundColor3 = Library.Scheme.AccentColor }):Play(); S.Color = Library.Scheme.AccentColor
                    else ITS:Create(Box, TweenInfo.new(0.2), { BackgroundColor3 = Library.Scheme.BackgroundColor }):Play(); S.Color = Library.Scheme.OutlineColor end
                    if not silent then for _, cb in ipairs(T.Callbacks) do pcall(cb, v) end; if Opt.Callback then pcall(Opt.Callback, v) end end
                end
                AddConn(Box.MouseButton1Click:Connect(function() Set(not State) end))
                AddConn(Box.MouseEnter:Connect(function() Lbl.TextColor3 = Color3.new(1,1,1) end))
                AddConn(Box.MouseLeave:Connect(function() Lbl.TextColor3 = Library.Scheme.FontColor end))
                function T:SetValue(v) Set(v) end
                function T:OnChanged(cb) table.insert(T.Callbacks, cb) end
                function T:GetState() return State end
                Library.Toggles[Flag] = T; Reg(Flag, T); return T
            end

            function G:AddSlider(Flag, Opt)
                Opt = Opt or {}; local Row = BaseRow(36)
                local S = { Value = Opt.Default or Opt.Min or 0, Min = Opt.Min or 0, Max = Opt.Max or 100, Type = "Slider", Callbacks = {}, Flag = Flag, Row = Row }
                INew("TextLabel", { Size = UDim2.new(1,-60,0,14), BackgroundTransparency = 1, Font = Enum.Font.Gotham, TextSize = 11, TextColor3 = Library.Scheme.FontColor, TextXAlignment = Enum.TextXAlignment.Left, Text = tostring(Opt.Text or Flag), Parent = Row })
                local function FormatNumber(num)
                    local r = Opt.Rounding or 0
                    if r == 0 then return tostring(math.floor(num + 0.5)) end
                    local mult = 10 ^ r
                    return tostring(math.floor(num * mult + 0.5) / mult)
                end
                local Val = INew("TextLabel", { Size = UDim2.new(0,50,0,14), Position = UDim2.new(1,-50,0,0), BackgroundTransparency = 1, Font = Enum.Font.GothamBold, TextSize = 11, TextColor3 = Color3.fromRGB(255,255,255), TextXAlignment = Enum.TextXAlignment.Right, Text = FormatNumber(S.Value), Parent = Row })
                local Track = INew("Frame", { Size = UDim2.new(1,0,0,6), Position = UDim2.new(0,0,0,24), BackgroundColor3 = Library.Scheme.BackgroundColor, BorderSizePixel = 0, Parent = Row })
                ICorner(Track, 3); IStroke(Track, Library.Scheme.OutlineColor, 1, 0.5)
                local Fill = INew("Frame", { Size = UDim2.new((S.Value-S.Min)/math.max(S.Max-S.Min,1e-9),0,1,0), BackgroundColor3 = Color3.fromRGB(255,255,255), BorderSizePixel = 0, Parent = Track })
                ICorner(Fill, 3); IGradient(Fill, Color3.fromRGB(255,255,255), Color3.fromRGB(100,100,100), 0)
                local Knob = INew("Frame", { Size = UDim2.new(0,12,0,12), AnchorPoint = Vector2.new(0.5,0.5), Position = UDim2.new((S.Value-S.Min)/math.max(S.Max-S.Min,1e-9),0,0.5,0), BackgroundColor3 = Color3.new(1,1,1), BorderSizePixel = 0, Parent = Track, ZIndex = 2 })
                ICorner(Knob, 6); IStroke(Knob, Color3.fromRGB(255,255,255), 2, 0)
                local dragging = false
                local function UpdateFromX(X)
                    local rel = math.clamp((X-Track.AbsolutePosition.X)/Track.AbsoluteSize.X, 0, 1)
                    local v = S.Min + (S.Max-S.Min)*rel
                    local r = Opt.Rounding or 0; local step = (r==0) and 1 or (10^-r); v = math.floor(v/step + 0.5)*step
                    if v ~= S.Value then
                        S.Value = v; Fill.Size = UDim2.new(rel,0,1,0); Knob.Position = UDim2.new(rel,0,0.5,0); Val.Text = FormatNumber(v)
                        for _, cb in ipairs(S.Callbacks) do pcall(cb, v) end; if Opt.Callback then pcall(Opt.Callback, v) end
                    end
                end
                AddConn(Track.InputBegan:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then dragging = true; UpdateFromX(i.Position.X) end end))
                AddConn(IUIS.InputChanged:Connect(function(i) if dragging and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then UpdateFromX(i.Position.X) end end))
                AddConn(IUIS.InputEnded:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then dragging = false end end))
                function S:SetValue(v) S.Value = v; Val.Text = FormatNumber(v); local rel = (v - S.Min) / math.max(S.Max - S.Min, 1e-9); Fill.Size = UDim2.new(rel,0,1,0); Knob.Position = UDim2.new(rel,0,0.5,0) end
                function S:OnChanged(cb) table.insert(S.Callbacks, cb) end
                Reg(Flag, S); return S
            end

            function G:AddDropdown(Flag, Opt)
                Opt = Opt or {}; local D = { Value = Opt.Default or (Opt.Multi and {} or nil), Values = Opt.Values or {}, Multi = Opt.Multi or false, Type = "Dropdown", Callbacks = {}, Flag = Flag, Expanded = false }
                local Row = BaseRow(28)
                local Btn = INew("TextButton", { Size = UDim2.new(1,0,0,26), BackgroundColor3 = Library.Scheme.BackgroundColor, BorderSizePixel = 0, Text = "", AutoButtonColor = false, Parent = Row })
                ICorner(Btn, 6); IStroke(Btn, Library.Scheme.OutlineColor, 1, 0.3)
                local function Display()
                    if D.Multi then local list = {}; for k, v in pairs(D.Value or {}) do if v then table.insert(list, k) end end; if #list == 0 then return tostring(Opt.Text or Flag) end; return table.concat(list, ", ") end
                    return tostring(D.Value or Opt.Text or Flag)
                end
                local Lbl = INew("TextLabel", { Size = UDim2.new(1,-24,1,0), Position = UDim2.new(0,10,0,0), BackgroundTransparency = 1, Font = Enum.Font.Gotham, TextSize = 11, TextColor3 = Library.Scheme.FontColor, TextXAlignment = Enum.TextXAlignment.Left, TextTruncate = Enum.TextTruncate.AtEnd, Text = Display(), Parent = Btn })
                local Chev = INew("TextLabel", { Size = UDim2.new(0,20,1,0), Position = UDim2.new(1,-22,0,0), BackgroundTransparency = 1, Font = Enum.Font.GothamBold, TextSize = 10, TextColor3 = Library.Scheme.TextMuted, Text = "▼", Parent = Btn })
                local Holder = INew("Frame", { Size = UDim2.new(1,0,0,0), BackgroundTransparency = 1, LayoutOrder = Next(), Parent = Body, Visible = false, AutomaticSize = Enum.AutomaticSize.Y })
                INew("UIListLayout", { SortOrder = Enum.SortOrder.LayoutOrder, Padding = UDim.new(0,3), Parent = Holder }); IPad(Holder, 4, 4, 4, 4)
                local itemBtns = {}
                local function build()
                    for _, b in ipairs(itemBtns) do b:Destroy() end; itemBtns = {}
                    for _, val in ipairs(D.Values) do
                        local IB = INew("TextButton", { Size = UDim2.new(1,0,0,24), BackgroundColor3 = Library.Scheme.TabColor, BorderSizePixel = 0, Text = "", AutoButtonColor = false, Parent = Holder })
                        ICorner(IB, 5)
                        INew("TextLabel", { Size = UDim2.new(1,-10,1,0), Position = UDim2.new(0,10,0,0), BackgroundTransparency = 1, Font = Enum.Font.Gotham, TextSize = 11, TextColor3 = Library.Scheme.FontColor, TextXAlignment = Enum.TextXAlignment.Left, Text = tostring(val), Parent = IB })
                        AddConn(IB.MouseEnter:Connect(function() IB.BackgroundColor3 = Library.Scheme.HoverColor end))
                        AddConn(IB.MouseLeave:Connect(function() IB.BackgroundColor3 = Library.Scheme.TabColor end))
                        AddConn(IB.MouseButton1Click:Connect(function()
                            if D.Multi then D.Value = D.Value or {}; D.Value[val] = not D.Value[val]
                            else D.Value = val; D.Expanded = false; Holder.Visible = false end
                            Lbl.Text = Display()
                            for _, cb in ipairs(D.Callbacks) do pcall(cb, D.Value) end; if Opt.Callback then pcall(Opt.Callback, D.Value) end
                        end))
                        table.insert(itemBtns, IB)
                    end
                end
                build()
                AddConn(Btn.MouseButton1Click:Connect(function()
                    D.Expanded = not D.Expanded; Holder.Visible = D.Expanded
                    ITS:Create(Chev, TweenInfo.new(0.2), { Rotation = D.Expanded and 180 or 0 }):Play()
                    if D.Expanded then build() end
                end))
                function D:SetValues(list) D.Values = list; build(); Lbl.Text = Display() end
                function D:SetValue(v) D.Value = v; Lbl.Text = Display() end
                function D:OnChanged(cb) table.insert(D.Callbacks, cb) end
                function D:GetValue() return D.Value end
                Reg(Flag, D); return D
            end

            function G:AddKeyPicker(Flag, Opt)
                Opt = Opt or {}; local K = { Value = Opt.Default or "None", Type = "KeyPicker", Callbacks = {}, Flag = Flag }
                local Row = BaseRow(26)
                local Box = INew("TextButton", { Size = UDim2.new(1,0,1,0), BackgroundColor3 = Library.Scheme.BackgroundColor, BorderSizePixel = 0, Text = "", AutoButtonColor = false, Parent = Row })
                ICorner(Box, 6); IStroke(Box, Library.Scheme.OutlineColor, 1, 0.3)
                local Lbl = INew("TextLabel", { Size = UDim2.new(1,-10,1,0), Position = UDim2.new(0,10,0,0), BackgroundTransparency = 1, Font = Enum.Font.Gotham, TextSize = 11, TextColor3 = Library.Scheme.FontColor, TextXAlignment = Enum.TextXAlignment.Left, Text = tostring(Opt.Text or Flag).." [ "..K.Value.." ]", Parent = Box })
                local LL = false
                AddConn(Box.MouseButton1Click:Connect(function() LL = true; Lbl.Text = tostring(Opt.Text or Flag).." [ ... ]"; Lbl.TextColor3 = Color3.fromRGB(255,255,255) end))
                AddConn(IUIS.InputBegan:Connect(function(input, gp)
                    if not LL or gp then return end
                    if input.UserInputType == Enum.UserInputType.Keyboard then
                        K.Value = input.KeyCode.Name; Lbl.Text = tostring(Opt.Text or Flag).." [ "..K.Value.." ]"; Lbl.TextColor3 = Library.Scheme.FontColor; LL = false
                        if Flag == "MenuKeybind" then Library.OpenKey = input.KeyCode end
                        for _, cb in ipairs(K.Callbacks) do pcall(cb, K.Value) end; if Opt.Callback then pcall(Opt.Callback, K.Value) end
                    end
                end))
                function K:GetState() return IUIS:IsKeyDown(Enum.KeyCode[K.Value]) and true or false end
                function K:SetValue(v) K.Value = v; Lbl.Text = tostring(Opt.Text or Flag).." [ "..v.." ]" end
                function K:OnChanged(cb) table.insert(K.Callbacks, cb) end
                Reg(Flag, K); return K
            end

            function G:AddButton(TextOrOpt, FuncOrOpt)
                local Row = BaseRow(28); local Text, Func
                if typeof(TextOrOpt) == "table" then Text = TextOrOpt.Text or "Button"; Func = TextOrOpt.Func
                else Text = TextOrOpt or "Button"; if typeof(FuncOrOpt) == "table" then Func = FuncOrOpt.Func else Func = FuncOrOpt end end
                local Btn = INew("TextButton", { Size = UDim2.new(1,0,1,0), BackgroundColor3 = Library.Scheme.BackgroundColor, BorderSizePixel = 0, Font = Enum.Font.Gotham, TextSize = 11, TextColor3 = Library.Scheme.FontColor, Text = tostring(Text), AutoButtonColor = false, Parent = Row })
                ICorner(Btn, 6); IStroke(Btn, Library.Scheme.OutlineColor, 1, 0.3)
                AddConn(Btn.MouseEnter:Connect(function() ITS:Create(Btn, TweenInfo.new(0.15), { BackgroundColor3 = Library.Scheme.HoverColor }):Play(); Btn.TextColor3 = Color3.new(1,1,1) end))
                AddConn(Btn.MouseLeave:Connect(function() ITS:Create(Btn, TweenInfo.new(0.15), { BackgroundColor3 = Library.Scheme.BackgroundColor }):Play(); Btn.TextColor3 = Library.Scheme.FontColor end))
                AddConn(Btn.MouseButton1Down:Connect(function() ITS:Create(Btn, TweenInfo.new(0.08), { BackgroundColor3 = Color3.fromRGB(255,255,255) }):Play(); Btn.TextColor3 = Color3.fromRGB(0,0,0) end))
                AddConn(Btn.MouseButton1Up:Connect(function() ITS:Create(Btn, TweenInfo.new(0.15), { BackgroundColor3 = Library.Scheme.HoverColor }):Play(); Btn.TextColor3 = Color3.new(1,1,1) end))
                AddConn(Btn.MouseButton1Click:Connect(function() if Func then pcall(Func) end end))
                return Btn
            end
            return G
        end

        local TW = {}
        function TW:AddLeftGroupbox(N) return MakeGroupbox(N, L, nil) end
        function TW:AddRightGroupbox(N) return MakeGroupbox(N, R, nil) end

        table.insert(Window.Tabs, TW)
        return TW
    end

    Library.Window = Window
    return Window
end

local Window = Library:CreateWindow({ Title = "PoniLOL", Subtitle = "Discord/Peaged" })

-- ===== CONFIG SYSTEM =====
local ConfigFolder = "PoniLOL_cfgXD"
local CurrentConfigName = "default"

local function GetConfigPath(name) return ConfigFolder .. "/" .. tostring(name) .. ".json" end

local function SerializeValue(v)
    local t = typeof(v)
    if t == "boolean" or t == "number" or t == "string" then return v end
    if t == "table" then
        local out = {}
        for k, val in pairs(v) do out[k] = SerializeValue(val) end
        return out
    end
    return nil
end

local function CollectConfig()
    local data = {}
    for flag, obj in pairs(Library.Options) do
        if obj.Type == "Toggle" then data[flag] = { type = "Toggle", value = obj.Value }
        elseif obj.Type == "Slider" then data[flag] = { type = "Slider", value = obj.Value }
        elseif obj.Type == "Dropdown" then data[flag] = { type = "Dropdown", value = SerializeValue(obj.Value) }
        elseif obj.Type == "KeyPicker" then data[flag] = { type = "KeyPicker", value = obj.Value } end
    end
    return data
end

local function ApplyConfig(data)
    if type(data) ~= "table" then return end
    for flag, entry in pairs(data) do
        local obj = Library.Options[flag]
        if obj and type(entry) == "table" then
            if entry.type == "Toggle" and obj.SetValue then pcall(function() obj:SetValue(entry.value) end)
            elseif entry.type == "Slider" and obj.SetValue then pcall(function() obj:SetValue(entry.value) end)
            elseif entry.type == "Dropdown" and obj.SetValue then
                if obj.Multi and type(entry.value) == "table" then
                    for k, v in pairs(entry.value) do if type(k) == "string" then obj.Value[k] = v end end
                else pcall(function() obj:SetValue(entry.value) end) end
            elseif entry.type == "KeyPicker" and obj.SetValue then pcall(function() obj:SetValue(entry.value) end) end
        end
    end
end

local function EnsureFolder()
    if makefolder and isfolder and not isfolder(ConfigFolder) then pcall(makefolder, ConfigFolder) end
end

local function SaveConfig(name)
    EnsureFolder()
    local path = GetConfigPath(name)
    local json = HttpService:JSONEncode(CollectConfig())
    pcall(writefile, path, json)
    return true
end

local function LoadConfig(name)
    local path = GetConfigPath(name)
    if not (isfile and isfile(path)) then return false end
    local ok, content = pcall(readfile, path)
    if not ok or not content then return false end
    local ok2, data = pcall(function() return HttpService:JSONDecode(content) end)
    if not ok2 or type(data) ~= "table" then return false end
    ApplyConfig(data)
    return true
end

local function ListConfigs()
    local list = {}
    if listfiles and isfolder and isfolder(ConfigFolder) then
        local ok, files = pcall(listfiles, ConfigFolder)
        if ok and files then
            for _, f in ipairs(files) do
                local n = f:match("([^/\\]+)%.json$")
                if n then table.insert(list, n) end
            end
        end
    end
    table.sort(list)
    return list
end

local function DeleteConfig(name)
    local path = GetConfigPath(name)
    if delfile and isfile and isfile(path) then pcall(delfile, path) end
end

local function OverdriveConfig(name)
    EnsureFolder()
    local path = GetConfigPath(name)
    local old = {}
    if isfile and isfile(path) then
        local ok, content = pcall(readfile, path)
        if ok and content then
            local ok2, d = pcall(function() return HttpService:JSONDecode(content) end)
            if ok2 and type(d) == "table" then old = d end
        end
    end
    local merged = {}
    for flag, obj in pairs(Library.Options) do
        local entry = old[flag]
        if entry and type(entry) == "table" and entry.value ~= nil then
            merged[flag] = { type = entry.type or obj.Type, value = entry.value }
        else
            if obj.Type == "Toggle" then merged[flag] = { type = "Toggle", value = obj.Value }
            elseif obj.Type == "Slider" then merged[flag] = { type = "Slider", value = obj.Value }
            elseif obj.Type == "Dropdown" then merged[flag] = { type = "Dropdown", value = SerializeValue(obj.Value) }
            elseif obj.Type == "KeyPicker" then merged[flag] = { type = "KeyPicker", value = obj.Value } end
        end
    end
    local json = HttpService:JSONEncode(merged)
    pcall(writefile, path, json)
    return true
end

getgenv().PoniLOL_Config = {
    Save = function(n) return SaveConfig(n or CurrentConfigName) end,
    Load = function(n) return LoadConfig(n or CurrentConfigName) end,
    Delete = function(n) return DeleteConfig(n or CurrentConfigName) end,
    Overdrive = function(n) return OverdriveConfig(n or CurrentConfigName) end,
    List = ListConfigs,
    SetName = function(n) CurrentConfigName = n end,
    GetName = function() return CurrentConfigName end,
}
-- ===== END CONFIG SYSTEM =====

local OrbitTab = Window:AddTab("orbitXD")
local OrbitBox = OrbitTab:AddLeftGroupbox("Orbit Player")

local orbitTarget = nil
local orbitConn = nil
local currentAngle = 0
local orbitRadius = 5.5
local orbitSpeed = 10
local heightOffset = 0.5

local function OrbitCleanup()
    if orbitConn then orbitConn:Disconnect(); orbitConn = nil end
    orbitTarget = nil
    local cam = WS.CurrentCamera
    if cam and ILP.Character then
        local hum = ILP.Character:FindFirstChildOfClass("Humanoid")
        if hum then cam.CameraSubject = hum end
    end
end

local function OrbitUpdate(dt)
    local cam = WS.CurrentCamera
    if not cam or not orbitTarget or not orbitTarget.Character or not ILP.Character then return end
    local eRoot = orbitTarget.Character:FindFirstChild("HumanoidRootPart")
    local eHum = orbitTarget.Character:FindFirstChildOfClass("Humanoid")
    local lRoot = ILP.Character:FindFirstChild("HumanoidRootPart")
    local lHum = ILP.Character:FindFirstChildOfClass("Humanoid")

    if eRoot and lRoot and eHum and lHum and lHum.Health > 0 and eHum.Health > 0 then
        if cam.CameraSubject ~= eHum then cam.CameraSubject = eHum end
        lRoot.Velocity = Vector3.new(0,0,0)
        lRoot.RotVelocity = Vector3.new(0,0,0)
        for _, part in ipairs(ILP.Character:GetChildren()) do
            if part:IsA("BasePart") then
                part.Velocity = Vector3.new(0,0,0)
                part.RotVelocity = Vector3.new(0,0,0)
            end
        end
        currentAngle = currentAngle + (orbitSpeed * dt)
        local ox = math.cos(currentAngle) * orbitRadius
        local oz = math.sin(currentAngle) * orbitRadius
        local targetPos = eRoot.Position + Vector3.new(ox, heightOffset, oz)
        lRoot.CFrame = CFrame.new(targetPos, eRoot.Position)
    end
end

local PlayerDropdown = OrbitBox:AddDropdown("OrbitTarget", { Text = "Select Player", Values = {} })
local function RefreshOrbitList()
    local list = {}
    for _, p in ipairs(IPS:GetPlayers()) do
        if p ~= ILP then table.insert(list, p.Name) end
    end
    table.sort(list)
    if PlayerDropdown and PlayerDropdown.SetValues then PlayerDropdown:SetValues(list) end
end
RefreshOrbitList()
IPS.PlayerAdded:Connect(RefreshOrbitList)
IPS.PlayerRemoving:Connect(RefreshOrbitList)

OrbitBox:AddSlider("OrbitRadius", { Text = "Radius", Default = 5.5, Min = 1, Max = 30, Rounding = 1, Callback = function(v) orbitRadius = v end })
OrbitBox:AddSlider("OrbitSpeed", { Text = "Speed", Default = 10, Min = 1, Max = 100, Rounding = 0, Callback = function(v) orbitSpeed = v end })
OrbitBox:AddSlider("OrbitHeight", { Text = "Height Offset", Default = 0.5, Min = -5, Max = 10, Rounding = 1, Callback = function(v) heightOffset = v end })

OrbitBox:AddToggleBox("OrbitActive_Toggle", { Text = "Start Orbit", Default = false, Callback = function(v)
    if v then
        local sel = Library.Options["OrbitTarget"]
        local name = sel and sel:GetValue()
        orbitTarget = name and IPS:FindFirstChild(name)
        if not orbitTarget then
            Library.Toggles["OrbitActive_Toggle"]:SetValue(false, true)
            return
        end
        currentAngle = 0
        if orbitConn then orbitConn:Disconnect() end
        orbitConn = RS.Heartbeat:Connect(OrbitUpdate)
    else
        OrbitCleanup()
    end
end })

local TechTab = Window:AddTab("Tech")
local TechBox = TechTab:AddLeftGroupbox("Loop Dash")

local dashEnabled = false
local dashDelay = 0.33
local isExecuting = false
local isCooldown = false
local isNoclipping = false
local activeDashConn = nil
local animConn = nil
local diedConn = nil

local function fireDash()
    local char = ILP.Character
    if not char then return end
    local communicate = char:FindFirstChild("Communicate")
    if communicate then
        local args = {{ Dash = Enum.KeyCode.W, Key = Enum.KeyCode.Q, Goal = "KeyPress" }}
        pcall(function() communicate:FireServer(unpack(args)) end)
    end
end

local function forceCancel()
    isNoclipping = false
    local char = ILP.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    local hum = char and char:FindFirstChildOfClass("Humanoid")
    if hum then hum.AutoRotate = true end
    if hrp then
        for _, obj in ipairs(hrp:GetChildren()) do
            if obj:IsA("BodyVelocity") or obj:IsA("LinearVelocity") or obj:IsA("BodyAngularVelocity") then
                obj:Destroy()
            end
        end
        hrp.AssemblyLinearVelocity = Vector3.zero
    end
end

local function resetDashState()
    isExecuting = false
    isCooldown = false
    isNoclipping = false
    if activeDashConn then
        activeDashConn:Disconnect()
        activeDashConn = nil
    end
    forceCancel()
end

local function getTorsoTarget()
    local char = ILP.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return nil end

    local params = OverlapParams.new()
    params.FilterType = Enum.RaycastFilterType.Exclude
    params.FilterDescendantsInstances = {char}

    local parts = WS:GetPartBoundsInRadius(hrp.Position, 8, params)
    local target, dist = nil, 8
    for _, part in ipairs(parts) do
        local model = part:FindFirstAncestorOfClass("Model")
        local tHum = model and model:FindFirstChildOfClass("Humanoid")
        if model and tHum and tHum.Health > 0 and model ~= char then
            local torso = model:FindFirstChild("Torso") or model:FindFirstChild("UpperTorso") or model:FindFirstChild("HumanoidRootPart")
            if torso then
                local d = (hrp.Position - torso.Position).Magnitude
                if d < dist then
                    dist = d
                    target = torso
                end
            end
        end
    end
    return target
end

local function executeLoopDash()
    if not dashEnabled or isExecuting or isCooldown then return end
    local char = ILP.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    local hum = char and char:FindFirstChildOfClass("Humanoid")
    if not root or not hum or hum.Health <= 0 then return end

    local torso = getTorsoTarget()
    if not torso then return end

    isExecuting = true
    isCooldown = true
    task.delay(5, function()
        isCooldown = false
    end)

    task.wait(dashDelay)

    if not dashEnabled or ILP.Character ~= char or not root.Parent or not hum.Parent or hum.Health <= 0 or not torso.Parent then
        isExecuting = false
        return
    end

    root.AssemblyLinearVelocity = Vector3.new(root.AssemblyLinearVelocity.X, 55, root.AssemblyLinearVelocity.Z)
    isNoclipping = true
    fireDash()

    local startT = tick()
    local diff = torso.Position - root.Position
    local flatDiff = Vector3.new(diff.X, 0, diff.Z)
    if flatDiff.Magnitude < 0.01 then
        forceCancel()
        isExecuting = false
        return
    end

    local forwardDir = flatDiff.Unit
    local sideVec = Vector3.new(-forwardDir.Z, 0, forwardDir.X)

    local bav = Instance.new("BodyAngularVelocity")
    bav.MaxTorque = Vector3.new(0, 1000000, 0)
    bav.P = 15000
    bav.Parent = root

    local function stopDash()
        if bav and bav.Parent then bav:Destroy() end
        if activeDashConn then
            activeDashConn:Disconnect()
            activeDashConn = nil
        end
        forceCancel()
        isExecuting = false
    end

    if activeDashConn then
        activeDashConn:Disconnect()
        activeDashConn = nil
    end

    activeDashConn = RS.Heartbeat:Connect(function()
        if not torso or not torso.Parent or not root or not root.Parent or not hum or not hum.Parent or hum.Health <= 0 or ILP.Character ~= char then
            stopDash()
            return
        end

        local elapsed = tick() - startT
        hum.AutoRotate = false

        local angle = (elapsed / 0.45) * (math.pi / 1.5)
        local radius = 15 * (1 - math.clamp(elapsed / 0.45, 0, 1))
        local targetLookPos = torso.Position + (sideVec * math.cos(angle) + forwardDir * math.sin(angle)) * radius
        local lookAtCF = CFrame.lookAt(root.Position, Vector3.new(targetLookPos.X, root.Position.Y, targetLookPos.Z))
        local relativeCF = root.CFrame:Inverse() * lookAtCF
        local _, y, _ = relativeCF:ToEulerAnglesXYZ()
        if bav and bav.Parent then
            bav.AngularVelocity = Vector3.new(0, y * 30, 0)
        end

        local distXZ = (Vector2.new(root.Position.X, root.Position.Z) - Vector2.new(torso.Position.X, torso.Position.Z)).Magnitude
        if (elapsed > 0.4 and distXZ < 2.2) or (not dashEnabled) or (elapsed > 1.5) then
            stopDash()
            return
        end
    end)
end

TechBox:AddToggleBox("LoopDash_Toggle", { Text = "Enable Loop Dash", Default = false, Callback = function(v)
    dashEnabled = v
    if not v then resetDashState() end
end })

local delaySlider = TechBox:AddSlider("LoopDash_Delay", { Text = "Delay", Default = 0.32, Min = 0.1, Max = 1, Rounding = 2, Callback = function(v) dashDelay = v end })
INew("TextLabel", {
    Size = UDim2.new(1,0,0,10), Position = UDim2.new(0,0,0,36),
    BackgroundTransparency = 1, Font = Enum.Font.GothamBold, TextSize = 10,
    TextColor3 = Color3.fromRGB(120,255,120), TextXAlignment = Enum.TextXAlignment.Left,
    Text = "0.32 (recommend)", Parent = delaySlider.Row,
})

RS.Stepped:Connect(function()
    if isNoclipping and ILP.Character then
        for _, model in ipairs(WS:GetChildren()) do
            if model:IsA("Model") and model ~= ILP.Character and model:FindFirstChildOfClass("Humanoid") then
                for _, part in ipairs(model:GetDescendants()) do
                    if part:IsA("BasePart") and part.CanCollide then
                        part.CanCollide = false
                    end
                end
            end
        end
    end
end)

local function setupDash(char)
    resetDashState()
    if animConn then animConn:Disconnect(); animConn = nil end
    if diedConn then diedConn:Disconnect(); diedConn = nil end

    char = char or ILP.Character
    if not char then return end

    local hum = char:WaitForChild("Humanoid", 10)
    if not hum then return end

    local animator = hum:FindFirstChildOfClass("Animator") or hum:WaitForChild("Animator", 5)

    diedConn = hum.Died:Connect(function()
        resetDashState()
    end)

    local function onAnimPlayed(track)
        if dashEnabled and track and track.Animation and track.Animation.AnimationId == "rbxassetid://10503381238" then
            task.spawn(executeLoopDash)
        end
    end

    if animator then
        animConn = animator.AnimationPlayed:Connect(onAnimPlayed)
    else
        animConn = hum.AnimationPlayed:Connect(onAnimPlayed)
    end
end

if ILP.Character then setupDash(ILP.Character) end
ILP.CharacterAdded:Connect(setupDash)

local TPTab = Window:AddTab("TP Player")
local TPBox = TPTab:AddLeftGroupbox("TP to Player")

local tpMode = "Up"
local tpTarget = nil
local tpConn = nil

local function clearTP()
    if tpConn then tpConn:Disconnect(); tpConn = nil end
    tpTarget = nil
end

local function teleportUpdate()
    if not tpTarget or not tpTarget.Character or not ILP.Character then return end
    local eRoot = tpTarget.Character:FindFirstChild("HumanoidRootPart")
    local lRoot = ILP.Character:FindFirstChild("HumanoidRootPart")
    if eRoot and lRoot then
        lRoot.Velocity = Vector3.new(0,0,0)
        lRoot.RotVelocity = Vector3.new(0,0,0)

        local basePos
        if tpMode == "Up" then
            basePos = eRoot.CFrame * CFrame.new(0, 5.5, 0)
            lRoot.CFrame = basePos * CFrame.Angles(math.rad(-90), 0, 0)
        else
            basePos = eRoot.CFrame * CFrame.new(0, -4.5, 0)
            lRoot.CFrame = basePos * CFrame.Angles(math.rad(90), 0, 0)
        end
    end
end

local TPDropdown = TPBox:AddDropdown("TPTargetList", { Text = "Select Player", Values = {} })
local function RefreshTPList()
    local list = {}
    for _, p in ipairs(IPS:GetPlayers()) do
        if p ~= ILP then table.insert(list, p.Name) end
    end
    table.sort(list)
    if TPDropdown and TPDropdown.SetValues then TPDropdown:SetValues(list) end
end
RefreshTPList()
IPS.PlayerAdded:Connect(RefreshTPList)
IPS.PlayerRemoving:Connect(RefreshTPList)

local modeBtn = TPBox:AddButton({ Text = "Switch Mode: Up", Func = function() end })
modeBtn.MouseButton1Click:Connect(function()
    tpMode = (tpMode == "Up") and "Down" or "Up"
    modeBtn.Text = "Switch Mode: " .. tpMode
end)

TPBox:AddToggleBox("TPActive_Toggle", { Text = "Start TP", Default = false, Callback = function(v)
    if v then
        local sel = Library.Options["TPTargetList"]
        local name = sel and sel:GetValue()
        tpTarget = name and IPS:FindFirstChild(name)
        if not tpTarget then
            Library.Toggles["TPActive_Toggle"]:SetValue(false, true)
            return
        end
        if tpConn then tpConn:Disconnect() end
        tpConn = RS.Heartbeat:Connect(teleportUpdate)
    else
        clearTP()
    end
end })

TPBox:AddButton({ Text = "Refresh Player List", Func = RefreshTPList })

local FlingTab = Window:AddTab("Fling")
local FlingBox = FlingTab:AddLeftGroupbox("Fling Player")

local FlingDropdown = FlingBox:AddDropdown("FlingTargetList", { Text = "Select Player", Values = {} })
local function RefreshFlingList()
    local list = {}
    for _, p in ipairs(IPS:GetPlayers()) do
        if p ~= ILP then table.insert(list, p.Name) end
    end
    table.sort(list)
    if FlingDropdown and FlingDropdown.SetValues then FlingDropdown:SetValues(list) end
end
RefreshFlingList()
IPS.PlayerAdded:Connect(RefreshFlingList)
IPS.PlayerRemoving:Connect(RefreshFlingList)

getgenv().OldPos = nil
getgenv().FPDH = WS.FallenPartsDestroyHeight
local FlingActive = false

local function SkidFling(TargetPlayer)
    local Character = ILP.Character
    local Humanoid = Character and Character:FindFirstChildOfClass("Humanoid")
    local RootPart = Humanoid and Humanoid.RootPart
    local TCharacter = TargetPlayer.Character
    if not TCharacter then return end

    local THumanoid = TCharacter:FindFirstChildOfClass("Humanoid")
    local TRootPart = THumanoid and THumanoid.RootPart
    local THead = TCharacter:FindFirstChild("Head")
    local Accessory = TCharacter:FindFirstChildOfClass("Accessory")
    local Handle = Accessory and Accessory:FindFirstChild("Handle")

    if not (Character and Humanoid and RootPart) then return end

    if RootPart.Velocity.Magnitude < 50 then
        getgenv().OldPos = RootPart.CFrame
    end

    if THumanoid and THumanoid.Sit then return end

    if THead then
        WS.CurrentCamera.CameraSubject = THead
    elseif Handle then
        WS.CurrentCamera.CameraSubject = Handle
    elseif THumanoid and TRootPart then
        WS.CurrentCamera.CameraSubject = THumanoid
    end

    if not TCharacter:FindFirstChildWhichIsA("BasePart") then return end

    local FPos = function(BasePart, Pos, Ang)
        RootPart.CFrame = CFrame.new(BasePart.Position) * Pos * Ang
        Character:SetPrimaryPartCFrame(CFrame.new(BasePart.Position) * Pos * Ang)
        RootPart.Velocity = Vector3.new(9e7, 9e7 * 10, 9e7)
        RootPart.RotVelocity = Vector3.new(9e8, 9e8, 9e8)
    end

    local SFBasePart = function(BasePart)
        local TimeToWait = 2
        local Time = tick()
        local Angle = 0
        repeat
            if RootPart and THumanoid then
                if BasePart.Velocity.Magnitude < 50 then
                    Angle = Angle + 100
                    FPos(BasePart, CFrame.new(0, 1.5, 0) + THumanoid.MoveDirection * BasePart.Velocity.Magnitude / 1.25, CFrame.Angles(math.rad(Angle), 0, 0))
                    task.wait()
                    FPos(BasePart, CFrame.new(0, -1.5, 0) + THumanoid.MoveDirection * BasePart.Velocity.Magnitude / 1.25, CFrame.Angles(math.rad(Angle), 0, 0))
                    task.wait()
                    FPos(BasePart, CFrame.new(0, 1.5, 0) + THumanoid.MoveDirection * BasePart.Velocity.Magnitude / 1.25, CFrame.Angles(math.rad(Angle), 0, 0))
                    task.wait()
                    FPos(BasePart, CFrame.new(0, -1.5, 0) + THumanoid.MoveDirection * BasePart.Velocity.Magnitude / 1.25, CFrame.Angles(math.rad(Angle), 0, 0))
                    task.wait()
                    FPos(BasePart, CFrame.new(0, 1.5, 0) + THumanoid.MoveDirection, CFrame.Angles(math.rad(Angle), 0, 0))
                    task.wait()
                    FPos(BasePart, CFrame.new(0, -1.5, 0) + THumanoid.MoveDirection, CFrame.Angles(math.rad(Angle), 0, 0))
                    task.wait()
                else
                    FPos(BasePart, CFrame.new(0, 1.5, THumanoid.WalkSpeed), CFrame.Angles(math.rad(90), 0, 0))
                    task.wait()
                    FPos(BasePart, CFrame.new(0, -1.5, -THumanoid.WalkSpeed), CFrame.Angles(0, 0, 0))
                    task.wait()
                    FPos(BasePart, CFrame.new(0, 1.5, THumanoid.WalkSpeed), CFrame.Angles(math.rad(90), 0, 0))
                    task.wait()
                    FPos(BasePart, CFrame.new(0, -1.5, 0), CFrame.Angles(math.rad(90), 0, 0))
                    task.wait()
                    FPos(BasePart, CFrame.new(0, -1.5, 0), CFrame.Angles(0, 0, 0))
                    task.wait()
                    FPos(BasePart, CFrame.new(0, -1.5, 0), CFrame.Angles(math.rad(90), 0, 0))
                    task.wait()
                    FPos(BasePart, CFrame.new(0, -1.5, 0), CFrame.Angles(0, 0, 0))
                    task.wait()
                end
            end
        until Time + TimeToWait < tick() or not FlingActive
    end

    WS.FallenPartsDestroyHeight = 0/0
    local BV = Instance.new("BodyVelocity")
    BV.Parent = RootPart
    BV.Velocity = Vector3.new(0, 0, 0)
    BV.MaxForce = Vector3.new(9e9, 9e9, 9e9)

    Humanoid:SetStateEnabled(Enum.HumanoidStateType.Seated, false)

    if TRootPart then
        SFBasePart(TRootPart)
    elseif THead then
        SFBasePart(THead)
    elseif Handle then
        SFBasePart(Handle)
    else
        return
    end

    BV:Destroy()
    Humanoid:SetStateEnabled(Enum.HumanoidStateType.Seated, true)
    WS.CurrentCamera.CameraSubject = Humanoid

    if getgenv().OldPos then
        repeat
            RootPart.CFrame = getgenv().OldPos * CFrame.new(0, .5, 0)
            Character:SetPrimaryPartCFrame(getgenv().OldPos * CFrame.new(0, .5, 0))
            Humanoid:ChangeState("GettingUp")
            for _, part in pairs(Character:GetChildren()) do
                if part:IsA("BasePart") then
                    part.Velocity, part.RotVelocity = Vector3.new(), Vector3.new()
                end
            end
            task.wait()
        until (RootPart.Position - getgenv().OldPos.p).Magnitude < 25
        WS.FallenPartsDestroyHeight = getgenv().FPDH
    end
end

FlingBox:AddToggleBox("FlingActive_Toggle", { Text = "Enable Fling", Default = false, Callback = function(v)
    FlingActive = v
    if v then
        local sel = Library.Options["FlingTargetList"]
        local name = sel and sel:GetValue()
        local target = name and IPS:FindFirstChild(name)
        if not target or not target.Character then
            Library.Toggles["FlingActive_Toggle"]:SetValue(false, true)
            return
        end

        task.spawn(function()
            while FlingActive do
                local currentTarget = IPS:FindFirstChild(name)
                if currentTarget and currentTarget.Character then
                    SkidFling(currentTarget)
                else
                    break
                end
                task.wait(0.1)
            end
            if Library.Toggles["FlingActive_Toggle"] then
                Library.Toggles["FlingActive_Toggle"]:SetValue(false, true)
            end
        end)
    end
end })

FlingBox:AddButton({ Text = "Refresh Player List", Func = RefreshFlingList })

-- ===== SETTINGS =====
local SettingsTab = Window:AddTab("Settings")
local CfgBox = SettingsTab:AddLeftGroupbox("Config")

local CfgDropdown = CfgBox:AddDropdown("ConfigList", { Text = "Select Config", Values = {} })

local function RefreshConfigList()
    local list = ListConfigs()
    if CfgDropdown and CfgDropdown.SetValues then CfgDropdown:SetValues(list) end
end
RefreshConfigList()

local InputRow = INew("Frame", { Size = UDim2.new(1,0,0,26), BackgroundTransparency = 1, LayoutOrder = 999, Parent = CfgBox.Body })
local CfgInput = INew("TextBox", { Size = UDim2.new(1,0,1,0), BackgroundColor3 = Library.Scheme.BackgroundColor, BorderSizePixel = 0, Font = Enum.Font.Gotham, TextSize = 11, TextColor3 = Library.Scheme.FontColor, PlaceholderText = "Config name...", PlaceholderColor3 = Library.Scheme.TextMuted, Text = "default", ClearTextOnFocus = false, Parent = InputRow })
ICorner(CfgInput, 6); IStroke(CfgInput, Library.Scheme.OutlineColor, 1, 0.3)
CfgInput:GetPropertyChangedSignal("Text"):Connect(function() CurrentConfigName = CfgInput.Text end)

CfgBox:AddButton({ Text = "Save Config", Func = function()
    SaveConfig(CurrentConfigName); RefreshConfigList()
    print("[Config] Saved: " .. CurrentConfigName)
end })

CfgBox:AddButton({ Text = "Load Config", Func = function()
    local sel = CfgDropdown:GetValue()
    local name = (sel and sel ~= "" and sel) or CurrentConfigName
    if LoadConfig(name) then
        CfgInput.Text = name
        print("[Config] Loaded: " .. name)
    else
        print("[Config] Load failed: " .. tostring(name))
    end
end })

CfgBox:AddButton({ Text = "Delete Config", Func = function()
    local sel = CfgDropdown:GetValue()
    local name = (sel and sel ~= "" and sel) or CurrentConfigName
    DeleteConfig(name); RefreshConfigList()
    print("[Config] Deleted: " .. tostring(name))
end })

CfgBox:AddButton({ Text = "Overdrive Config", Func = function()
    local sel = CfgDropdown:GetValue()
    local name = (sel and sel ~= "" and sel) or CurrentConfigName
    if OverdriveConfig(name) then
        RefreshConfigList()
        print("[Config] Overdrived: " .. tostring(name))
    end
end })

CfgBox:AddButton({ Text = "Refresh List", Func = RefreshConfigList })

local MenuBox = SettingsTab:AddLeftGroupbox("Menu")
MenuBox:AddKeyPicker("MenuKeybind", { Text = "Open/close GUI", Default = "RightShift" })
MenuBox:AddButton({ Text = "Unload Script", Func = function()
    if Library.ScreenGui then Library.ScreenGui:Destroy() end
end })
