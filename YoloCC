--////////////////////////////////////////////////////
-- YoloCC Engine System
-- Single Clean Script
--////////////////////////////////////////////////////

-- SERVICES
local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera

local LP = Players.LocalPlayer

--====================================================
-- PRE-UI TOGGLE KEY PICKER
--====================================================
local UIToggleKey = Enum.KeyCode.RightShift
local pickingKey = true

do
    local tempGui = Instance.new("ScreenGui", game.CoreGui)
    tempGui.Name = "YoloCC_KeyPicker"

    local f = Instance.new("Frame", tempGui)
    f.Size = UDim2.fromScale(0.25,0.18)
    f.Position = UDim2.fromScale(0.375,0.41)
    f.BackgroundColor3 = Color3.fromRGB(15,15,15)
    f.BorderSizePixel = 0

    local txt = Instance.new("TextLabel", f)
    txt.Size = UDim2.fromScale(1,1)
    txt.Text = "Press a key to toggle UI"
    txt.Font = Enum.Font.GothamBold
    txt.TextSize = 18
    txt.TextColor3 = Color3.new(1,1,1)
    txt.BackgroundTransparency = 1

    local conn
    conn = UIS.InputBegan:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.Keyboard then
            UIToggleKey = i.KeyCode
            pickingKey = false
            conn:Disconnect()
            tempGui:Destroy()
        end
    end)
end

repeat task.wait() until not pickingKey

--====================================================
-- STATES
--====================================================
local UIVisible = true

local StickyEnabled = false
local Prediction = 0.3
local CamSmooth = 0.15
local LockKey = Enum.KeyCode.T

local WalkEnabled = false
local WalkSpeedValue = 0
local WalkKey = Enum.KeyCode.C
local DefaultWalkSpeed = 16

local NameESP = false
local LockedTarget = nil

--====================================================
-- UI ROOT
--====================================================
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "YoloCC"
gui.Enabled = true

local main = Instance.new("Frame", gui)
main.Size = UDim2.fromScale(0.45,0.45)
main.Position = UDim2.fromScale(0.275,0.275)
main.BackgroundColor3 = Color3.fromRGB(15,15,15)
main.BorderSizePixel = 0

--====================================================
-- TOP BAR
--====================================================
local top = Instance.new("Frame", main)
top.Size = UDim2.fromScale(1,0.1)
top.BackgroundColor3 = Color3.fromRGB(20,20,20)
top.BorderSizePixel = 0

local title = Instance.new("TextLabel", top)
title.Size = UDim2.fromScale(1,1)
title.Text = "YoloCC"
title.Font = Enum.Font.GothamBold
title.TextSize = 18
title.TextColor3 = Color3.new(1,1,1)
title.BackgroundTransparency = 1

--====================================================
-- DRAG SYSTEM (TOP BAR ONLY, MOVES WHOLE UI)
--====================================================
local dragging = false
local dragStart
local startPos

top.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = main.Position
    end
end)

top.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)

UIS.InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - dragStart
        main.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
    end
end)

--====================================================
-- BODY
--====================================================
local body = Instance.new("Frame", main)
body.Position = UDim2.fromScale(0,0.1)
body.Size = UDim2.fromScale(1,0.9)
body.BackgroundTransparency = 1

--====================================================
-- TAB BAR
--====================================================
local tabBar = Instance.new("Frame", body)
tabBar.Size = UDim2.fromScale(1,0.15)
tabBar.BackgroundTransparency = 1

local pages = Instance.new("Frame", body)
pages.Position = UDim2.fromScale(0,0.15)
pages.Size = UDim2.fromScale(1,0.85)
pages.BackgroundTransparency = 1

local tabs = {}

local function createTab(name,pos)
    local b = Instance.new("TextButton", tabBar)
    b.Size = UDim2.fromScale(0.32,0.8)
    b.Position = UDim2.fromScale(pos,0.1)
    b.Text = name
    b.Font = Enum.Font.Gotham
    b.TextSize = 14
    b.TextColor3 = Color3.new(1,1,1)
    b.BackgroundColor3 = Color3.fromRGB(25,25,25)
    b.BorderSizePixel = 0

    local page = Instance.new("Frame", pages)
    page.Size = UDim2.fromScale(1,1)
    page.Visible = false
    page.BackgroundTransparency = 1

    tabs[b] = page

    b.MouseButton1Click:Connect(function()
        for _,p in pairs(pages:GetChildren()) do p.Visible = false end
        for t,_ in pairs(tabs) do t.BackgroundColor3 = Color3.fromRGB(25,25,25) end
        b.BackgroundColor3 = Color3.fromRGB(35,35,35)
        page.Visible = true
    end)

    return page
end

local aimPage = createTab("AIM",0.02)
local espPage = createTab("ESP",0.34)
local playerPage = createTab("PLAYER",0.66)
aimPage.Visible = true

--====================================================
-- UI HELPERS
--====================================================
local function engineButton(parent,text,y,callback)
    local b = Instance.new("TextButton", parent)
    b.Size = UDim2.fromScale(0.9,0.12)
    b.Position = UDim2.fromScale(0.05,y)
    b.Text = text
    b.Font = Enum.Font.Gotham
    b.TextSize = 14
    b.TextColor3 = Color3.new(1,1,1)
    b.BackgroundColor3 = Color3.fromRGB(25,25,25)
    b.BorderSizePixel = 0
    b.MouseButton1Click:Connect(callback)
    return b
end

local function engineSlider(parent,text,y,min,max,value,callback)
    local f = Instance.new("Frame", parent)
    f.Size = UDim2.fromScale(0.9,0.14)
    f.Position = UDim2.fromScale(0.05,y)
    f.BackgroundTransparency = 1

    local label = Instance.new("TextLabel", f)
    label.Size = UDim2.fromScale(1,0.4)
    label.Text = text.." = "..value
    label.Font = Enum.Font.Code
    label.TextSize = 13
    label.TextColor3 = Color3.new(1,1,1)
    label.BackgroundTransparency = 1
    label.TextXAlignment = Enum.TextXAlignment.Left

    local bar = Instance.new("Frame", f)
    bar.Position = UDim2.fromScale(0,0.55)
    bar.Size = UDim2.fromScale(1,0.25)
    bar.BackgroundColor3 = Color3.fromRGB(30,30,30)
    bar.BorderSizePixel = 0

    local fill = Instance.new("Frame", bar)
    fill.Size = UDim2.fromScale((value-min)/(max-min),1)
    fill.BackgroundColor3 = Color3.fromRGB(120,180,255)
    fill.BorderSizePixel = 0

    local dragging = false
    bar.InputBegan:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 then dragging = true end
    end)
    UIS.InputEnded:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end
    end)

    RunService.RenderStepped:Connect(function()
        if dragging then
            local x = math.clamp((UIS:GetMouseLocation().X - bar.AbsolutePosition.X)/bar.AbsoluteSize.X,0,1)
            fill.Size = UDim2.fromScale(x,1)
            local val = math.floor((min+(max-min)*x)*100)/100
            label.Text = text.." = "..val
            callback(val)
        end
    end)
end

--====================================================
-- AIM TAB
--====================================================
local stickyBtn
stickyBtn = engineButton(aimPage,"Sticky Lock",0.05,function()
    StickyEnabled = not StickyEnabled
    if StickyEnabled then
        stickyBtn.BackgroundColor3 = Color3.fromRGB(0,120,0)
    else
        stickyBtn.BackgroundColor3 = Color3.fromRGB(25,25,25)
        LockedTarget = nil
    end
end)

engineSlider(aimPage,"Prediction",0.22,0,1,Prediction,function(v)
    Prediction = v
end)

engineSlider(aimPage,"Camera Smooth",0.42,0.01,1,CamSmooth,function(v)
    CamSmooth = v
end)

local keyLabel
keyLabel = engineButton(aimPage,"Lock Key = T",0.62,function()
    keyLabel.Text = "Press a key..."
    local conn
    conn = UIS.InputBegan:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.Keyboard then
            LockKey = i.KeyCode
            keyLabel.Text = "Lock Key = "..i.KeyCode.Name
            conn:Disconnect()
        end
    end)
end)

--====================================================
-- FIXED PREDICTION GRAPH ENGINE
--====================================================
local graphFrame = Instance.new("Frame", aimPage)
graphFrame.Size = UDim2.fromScale(0.9,0.22)
graphFrame.Position = UDim2.fromScale(0.05,0.78)
graphFrame.BackgroundColor3 = Color3.fromRGB(18,18,18)
graphFrame.BorderSizePixel = 0
graphFrame.ClipsDescendants = true -- 🔥 FIX: prevents overflow

local baseLine = Instance.new("Frame", graphFrame)
baseLine.Size = UDim2.fromScale(1,0.02)
baseLine.Position = UDim2.fromScale(0,0.9)
baseLine.BackgroundColor3 = Color3.fromRGB(220,220,220)
baseLine.BorderSizePixel = 0

--========================
-- SCROLLBAR
--========================
local scrollBar = Instance.new("Frame", graphFrame)
scrollBar.Size = UDim2.fromScale(1,0.1)
scrollBar.Position = UDim2.fromScale(0,0.9)
scrollBar.BackgroundColor3 = Color3.fromRGB(25,25,25)
scrollBar.BorderSizePixel = 0

local scrollHandle = Instance.new("Frame", scrollBar)
scrollHandle.Size = UDim2.fromScale(0.12,1)
scrollHandle.Position = UDim2.fromScale(0.88,0)
scrollHandle.BackgroundColor3 = Color3.fromRGB(120,180,255)
scrollHandle.BorderSizePixel = 0

local scrolling = false
local scrollValue = 1 -- 1 = live, 0 = oldest

scrollHandle.InputBegan:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 then
        scrolling = true
    end
end)

UIS.InputEnded:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 then
        scrolling = false
    end
end)

UIS.InputChanged:Connect(function(i)
    if scrolling and i.UserInputType == Enum.UserInputType.MouseMovement then
        local x = (UIS:GetMouseLocation().X - scrollBar.AbsolutePosition.X) / scrollBar.AbsoluteSize.X
        x = math.clamp(x, 0, 1)
        scrollHandle.Position = UDim2.fromScale(x - (scrollHandle.Size.X.Scale/2), 0)
        scrollValue = x
    end
end)

--========================
-- GRAPH BUFFER
--========================
local graphBuffer = {}
local MAX_POINTS = 220

local function createPoint(color, size)
    local p = Instance.new("Frame")
    p.Size = UDim2.new(0,size,0,size)
    p.BackgroundColor3 = color
    p.BorderSizePixel = 0
    p.Parent = graphFrame
    return p
end

--========================
-- GRAPH ENGINE LOOP
--========================
RunService.RenderStepped:Connect(function()
    -- stop recording if not locked
    if not StickyEnabled or not LockedTarget then return end
    if not LockedTarget.Character or not LockedTarget.Character:FindFirstChild("HumanoidRootPart") then return end

    local hrp = LockedTarget.Character.HumanoidRootPart

    local mouseY = UIS:GetMouseLocation().Y
    local screenY = Camera.ViewportSize.Y
    local cursorNorm = math.clamp(mouseY / screenY, 0, 1)

    local velY = hrp.Velocity.Y
    local jumpDetected = velY > 35

    -- shift buffer
    for i,v in ipairs(graphBuffer) do
        v.t = v.t - 1
    end

    -- remove overflow
    while #graphBuffer > MAX_POINTS do
        if graphBuffer[1].ui then
            graphBuffer[1].ui:Destroy()
        end
        table.remove(graphBuffer,1)
    end

    -- add cursor point (blue)
    local blue = createPoint(Color3.fromRGB(0,180,255), 2)
    table.insert(graphBuffer,{
        ui = blue,
        y = cursorNorm,
        t = MAX_POINTS
    })

    -- add jump point (red)
    if jumpDetected then
        local red = createPoint(Color3.fromRGB(255,0,0), 4)
        table.insert(graphBuffer,{
            ui = red,
            y = cursorNorm,
            t = MAX_POINTS
        })
    end

    -- render with scroll
    local scrollOffset = math.floor((1 - scrollValue) * MAX_POINTS)

    for i,v in ipairs(graphBuffer) do
        if v.ui then
            local xIndex = v.t - scrollOffset
            if xIndex >= 0 and xIndex <= MAX_POINTS then
                local x = xIndex / MAX_POINTS
                local y = 0.9 - (v.y * 0.8)

                v.ui.Position = UDim2.fromScale(x, y)
                v.ui.Visible = true
            else
                v.ui.Visible = false
            end
        end
    end
end)



-- graph buffers
local graphPoints = {}
local maxPoints = 140

local function createPoint(color)
    local p = Instance.new("Frame", graphFrame)
    p.Size = UDim2.new(0,2,0,2)
    p.BackgroundColor3 = color
    p.BorderSizePixel = 0
    return p
end

-- graph engine
RunService.RenderStepped:Connect(function()
    if not StickyEnabled or not LockedTarget then return end
    if not LockedTarget.Character or not LockedTarget.Character:FindFirstChild("HumanoidRootPart") then return end

    local hrp = LockedTarget.Character.HumanoidRootPart

    local mouseY = UIS:GetMouseLocation().Y
    local screenY = Camera.ViewportSize.Y
    local cursorNorm = math.clamp(mouseY / screenY, 0, 1)

    local velY = hrp.Velocity.Y
    local jumpDetected = velY > 35

    for i,v in ipairs(graphPoints) do
        v.x = v.x - (1/maxPoints)
    end

    while #graphPoints > maxPoints do
        if graphPoints[1].ui then graphPoints[1].ui:Destroy() end
        table.remove(graphPoints,1)
    end

    local blue = createPoint(Color3.fromRGB(0,180,255))
    blue.Position = UDim2.fromScale(1, 0.9 - (cursorNorm * 0.8))
    table.insert(graphPoints,{ui=blue,x=1})

    if jumpDetected then
        local red = createPoint(Color3.fromRGB(255,0,0))
        red.Size = UDim2.new(0,4,0,4)
        red.Position = UDim2.fromScale(1, 0.9 - (cursorNorm * 0.8))
        table.insert(graphPoints,{ui=red,x=1})
    end

    local offset = (1 - scrollPos) * 0.6
    for _,p in ipairs(graphPoints) do
        if p.ui then
            p.ui.Position = UDim2.fromScale(p.x - offset, p.ui.Position.Y.Scale)
        end
    end
end)

--====================================================
-- ESP TAB (NAME ONLY)
--====================================================
engineButton(espPage,"Name ESP",0.05,function()
    NameESP = not NameESP
end)

local espFolder = Instance.new("Folder",gui)
espFolder.Name = "NameESP"

local function createNameESP(p)
    if p == LP then return end
    local tag = Instance.new("BillboardGui", espFolder)
    tag.AlwaysOnTop = true
    tag.Size = UDim2.new(0,220,0,45)

    local txt = Instance.new("TextLabel", tag)
    txt.Size = UDim2.new(1,0,1,0)
    txt.Text = p.DisplayName
    txt.Font = Enum.Font.GothamBold
    txt.TextSize = 13
    txt.TextColor3 = Color3.new(1,1,1)
    txt.BackgroundTransparency = 1
    txt.TextStrokeTransparency = 0
    txt.TextStrokeColor3 = Color3.new(0,0,0)

    RunService.RenderStepped:Connect(function()
        if NameESP and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
            tag.Adornee = p.Character.HumanoidRootPart
            tag.Enabled = true
        else
            tag.Enabled = false
        end
    end)
end

for _,p in pairs(Players:GetPlayers()) do createNameESP(p) end
Players.PlayerAdded:Connect(createNameESP)

--====================================================
-- PLAYER TAB
--====================================================
engineButton(playerPage,"WalkSpeed Toggle",0.05,function()
    WalkEnabled = not WalkEnabled
end)

engineSlider(playerPage,"WalkSpeed",0.25,0,1000,0,function(v)
    WalkSpeedValue = v
end)

local walkKeyBtn
walkKeyBtn = engineButton(playerPage,"Walk Key = C",0.45,function()
    walkKeyBtn.Text = "Press a key..."
    local conn
    conn = UIS.InputBegan:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.Keyboard then
            WalkKey = i.KeyCode
            walkKeyBtn.Text = "Walk Key = "..i.KeyCode.Name
            conn:Disconnect()
        end
    end)
end)

--====================================================
-- INPUT
--====================================================
UIS.InputBegan:Connect(function(i,gp)
    if gp then return end

    if i.KeyCode == UIToggleKey then
        UIVisible = not UIVisible
        gui.Enabled = UIVisible
    end

    if i.KeyCode == LockKey then
        StickyEnabled = not StickyEnabled
        if StickyEnabled then
            stickyBtn.BackgroundColor3 = Color3.fromRGB(0,120,0)
        else
            stickyBtn.BackgroundColor3 = Color3.fromRGB(25,25,25)
            LockedTarget = nil
        end
    end

    if i.KeyCode == WalkKey then
        WalkEnabled = not WalkEnabled
    end
end)

--====================================================
-- LOGIC LOOPS
--====================================================
RunService.RenderStepped:Connect(function()
    if WalkEnabled and LP.Character and LP.Character:FindFirstChild("Humanoid") then
        if WalkSpeedValue == 0 then
            LP.Character.Humanoid.WalkSpeed = DefaultWalkSpeed
        else
            LP.Character.Humanoid.WalkSpeed = WalkSpeedValue
        end
    end
end)

local function getClosest()
    local closest,dist
    for _,p in pairs(Players:GetPlayers()) do
        if p~=LP and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
            local pos,vis = Camera:WorldToViewportPoint(p.Character.HumanoidRootPart.Position)
            if vis then
                local d=(Vector2.new(pos.X,pos.Y)-UIS:GetMouseLocation()).Magnitude
                if not dist or d<dist then
                    dist=d
                    closest=p
                end
            end
        end
    end
    return closest
end

RunService.RenderStepped:Connect(function()
    if StickyEnabled then
        if not LockedTarget then LockedTarget=getClosest() end
        if LockedTarget and LockedTarget.Character and LockedTarget.Character:FindFirstChild("HumanoidRootPart") then
            local hrp=LockedTarget.Character.HumanoidRootPart
            local predicted=hrp.Position+(hrp.Velocity*Prediction)
            Camera.CFrame=Camera.CFrame:Lerp(
                CFrame.new(Camera.CFrame.Position,predicted),
                CamSmooth
            )
        end
    end
end)
