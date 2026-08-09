if getgenv().GoonWaresExecuted then
    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = "WARNING!",
        Text = "Script Is Already Loaded, rejoin if you want to re-execute.",
        Duration = 8,
    })
    return
end
getgenv().GoonWaresExecuted = true

Workspace = game:GetService("Workspace")
RunService = game:GetService("RunService")
Players = game:GetService("Players")
Lighting = game:GetService("Lighting")
StarterGui = game:GetService("StarterGui")
ReplicatedStorage = game:GetService("ReplicatedStorage")
Camera = Workspace.CurrentCamera
LocalPlayer = Players.LocalPlayer
UserInputService = game:GetService("UserInputService")
TweenService = game:GetService("TweenService")
PathfindingService = game:GetService("PathfindingService")
CAS = game:GetService("ContextActionService")
HttpService = game:GetService("HttpService")
TeleportService = game:GetService("TeleportService")
MarketplaceService = game:GetService("MarketplaceService")
Stats = game:GetService("Stats")

local Fluent = loadstring(game:HttpGet("https://github.com/StyearX/GoonWares/releases/download/FluentPro/Main.lua"))()

isMobile = UserInputService.TouchEnabled and not UserInputService.MouseEnabled and not UserInputService.KeyboardEnabled

function Notify(Title, Content, NType, Icon, Duration)
    Fluent:Notify({ Title = Title, Content = Content, Type = NType or "Info", Icon = Icon, Duration = Duration or 3 })
end

task.spawn(function()
    local ExecutorName, ExecutorVersion = "Unknown", ""
    local Success = pcall(function()
        if identifyexecutor then
            ExecutorName, ExecutorVersion = identifyexecutor()
        elseif getexecutorname then
            ExecutorName = getexecutorname()
        end
    end)

    Notify(
        "GoonWares",
        string.format("Player: %s | Executor: %s %s", LocalPlayer.Name, ExecutorName or "Unknown", ExecutorVersion or ""),
        "Info",
        nil,
        6
    )
end)

OpenShit = Instance.new("ScreenGui")
OpenShit.Name = "OpenShit"
OpenShit.Parent = LocalPlayer:WaitForChild("PlayerGui")
OpenShit.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
OpenShit.ResetOnSpawn = false

MainOpen = Instance.new("TextButton")
MainOpen.Name = "MainOpen"
MainOpen.Parent = OpenShit
MainOpen.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
MainOpen.BackgroundTransparency = 1
MainOpen.Position = UDim2.new(0.101969875, 0, 0.110441767, 0)
MainOpen.Size = UDim2.new(0, 64, 0, 42)
MainOpen.Text = ""
MainOpen.Visible = true

MainOpenCorner = Instance.new("UICorner")
MainOpenCorner.Parent = MainOpen

SizeBackMulti = 0.1
AssetsIcon = "rbxassetid://139095000385640"
AssetsBackground = "rbxassetid://109694296016043"

BackgroundImage = Instance.new("ImageLabel")
BackgroundImage.Name = "RotatingBackground"
BackgroundImage.Parent = MainOpen
BackgroundImage.Size = UDim2.new(2.3 + SizeBackMulti, 0, 2.3 + SizeBackMulti, 0)
BackgroundImage.Position = UDim2.new(0.5, 0, 0.5, 0)
BackgroundImage.AnchorPoint = Vector2.new(0.5, 0.5)
BackgroundImage.BackgroundTransparency = 1
BackgroundImage.Image = AssetsBackground
BackgroundImage.SizeConstraint = Enum.SizeConstraint.RelativeXX
BackgroundImage.ZIndex = 0

FrontImage = Instance.new("ImageLabel")
FrontImage.Name = "StaticIcon"
FrontImage.Parent = MainOpen
FrontImage.Size = UDim2.new(0.8, 0, 1.2, 0)
FrontImage.Position = UDim2.new(0.5, 0, 0.5, 0)
FrontImage.AnchorPoint = Vector2.new(0.5, 0.5)
FrontImage.BackgroundTransparency = 1
FrontImage.Image = AssetsIcon
FrontImage.ZIndex = 1

FrontCorner = Instance.new("UICorner")
FrontCorner.CornerRadius = UDim.new(1, 0)
FrontCorner.Parent = FrontImage

local Rotation = 0
local Speed = 90
local LastTime = tick()

task.spawn(function()
    while true do
        local Now = tick()
        local Delta = Now - LastTime
        LastTime = Now
        Rotation = (Rotation + Speed * Delta) % 360
        BackgroundImage.Rotation = Rotation
        task.wait()
    end
end)

function MakeDraggable(TopbarObject, Object, Locked)
    local Dragging = false
    local DragInput
    local DragStart
    local StartPosition
    local Holding = false
    local HoldTime = 1.0
    local MoveCancelThreshold = 6
    local HoldToken = 0

    Object:SetAttribute("Locked", Locked or false)

    local function Update(Input)
        if Object:GetAttribute("Locked") then return end
        local Delta = Input.Position - DragStart
        Object.Position = UDim2.new(
            StartPosition.X.Scale,
            StartPosition.X.Offset + Delta.X,
            StartPosition.Y.Scale,
            StartPosition.Y.Offset + Delta.Y
        )
    end

    local function ToggleLock()
        local NewState = not Object:GetAttribute("Locked")
        Object:SetAttribute("Locked", NewState)
        Fluent:Notify({
            Title = NewState and "Button Locked" or "Button Unlocked",
            Content = NewState and "This button is now locked in place." or "This button can now be moved.",
            Duration = 2
        })
    end

    TopbarObject.InputBegan:Connect(function(Input)
        if Input.UserInputType ~= Enum.UserInputType.MouseButton1 and Input.UserInputType ~= Enum.UserInputType.Touch then return end
        Dragging = not Object:GetAttribute("Locked")
        Holding = true
        DragStart = Input.Position
        StartPosition = Object.Position
        HoldToken += 1
        local Token = HoldToken
        task.delay(HoldTime, function()
            if Holding and Token == HoldToken then ToggleLock() end
        end)
        Input.Changed:Connect(function()
            if Input.UserInputState == Enum.UserInputState.End then
                Dragging = false
                Holding = false
            end
        end)
    end)

    TopbarObject.InputChanged:Connect(function(Input)
        if not DragStart then return end
        if Input.UserInputType == Enum.UserInputType.MouseMovement or Input.UserInputType == Enum.UserInputType.Touch then
            if (Input.Position - DragStart).Magnitude > MoveCancelThreshold then Holding = false end
            DragInput = Input
        end
    end)

    UserInputService.InputChanged:Connect(function(Input)
        if Input == DragInput and Dragging then Update(Input) end
    end)
end

MakeDraggable(MainOpen, MainOpen, false)

local function PlaySound(SoundId)
    local Sound = Instance.new("Sound")
    Sound.SoundId = "rbxassetid://" .. SoundId
    Sound.Parent = game:GetService("SoundService")
    Sound:Play()
    Sound.Ended:Connect(function()
        Sound:Destroy()
    end)
end

MainOpen.MouseButton1Click:Connect(function()
    local Sounds = { "7127123605", "137566474343039", "438666542", "257001341", "257000833", "7127123554", "131607746976396", "97325669841459", "109312518223078" }
    PlaySound(Sounds[math.random(#Sounds)])
    Window:Minimize()
    local function SmoothSpeed(Target, Duration)
        local Start = Speed
        local Steps = 30
        for I = 1, Steps do
            Speed = Start + (Target - Start) * (I / Steps)
            task.wait(Duration / Steps)
        end
        Speed = Target
    end
    SmoothSpeed(360, 0.4)
    task.wait(0.5)
    SmoothSpeed(180, 0.4)
    task.wait(0.3)
    SmoothSpeed(90, 0.4)
end)

Fluent:RegisterCustomTheme("AzureLights", {
    Accent = Color3.fromRGB(70, 130, 255),
    AcrylicMain = Color3.fromRGB(248, 248, 248),
    AcrylicBorder = Color3.fromRGB(200, 200, 200),
    AcrylicGradient = ColorSequence.new({
        ColorSequenceKeypoint.new(0, Color3.fromRGB(50, 150, 255)),
        ColorSequenceKeypoint.new(0.2, Color3.fromRGB(100, 200, 255)),
        ColorSequenceKeypoint.new(0.4, Color3.fromRGB(160, 230, 255)),
        ColorSequenceKeypoint.new(0.6, Color3.fromRGB(220, 250, 255)),
        ColorSequenceKeypoint.new(0.8, Color3.fromRGB(180, 220, 255)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(50, 150, 255))
    }),
    AcrylicNoise = 0.91,
    TitleBarLine = Color3.fromRGB(200, 200, 200),
    Tab = Color3.fromRGB(235, 235, 235),
    Element = Color3.fromRGB(235, 235, 235),
    ElementBorder = Color3.fromRGB(180, 180, 180),
    InElementBorder = Color3.fromRGB(200, 200, 200),
    ElementTransparency = 0.9,
    ElementBorderThickness = 1,
    ToggleSlider = Color3.fromRGB(200, 200, 200),
    ToggleToggled = Color3.fromRGB(70, 130, 255),
    SliderRail = Color3.fromRGB(200, 200, 200),
    CheckboxUnchecked = Color3.fromRGB(200, 200, 200),
    CheckboxChecked = Color3.fromRGB(70, 130, 255),
    CheckboxCheck = Color3.fromRGB(255, 255, 255),
    ProgressBarRail = Color3.fromRGB(200, 200, 200),
    ProgressBarFill = Color3.fromRGB(70, 130, 255),
    DropdownFrame = Color3.fromRGB(255, 255, 255),
    DropdownHolder = Color3.fromRGB(240, 240, 240),
    DropdownBorder = Color3.fromRGB(180, 180, 180),
    DropdownOption = Color3.fromRGB(235, 235, 235),
    DropdownBorderThickness = 1,
    Keybind = Color3.fromRGB(235, 235, 235),
    Input = Color3.fromRGB(255, 255, 255),
    InputFocused = Color3.fromRGB(255, 255, 255),
    InputIndicator = Color3.fromRGB(70, 130, 255),
    Dialog = Color3.fromRGB(255, 255, 255),
    DialogHolder = Color3.fromRGB(240, 240, 240),
    DialogHolderLine = Color3.fromRGB(200, 200, 200),
    DialogButton = Color3.fromRGB(248, 248, 248),
    DialogButtonBorder = Color3.fromRGB(200, 200, 200),
    DialogBorder = Color3.fromRGB(180, 180, 180),
    DialogInput = Color3.fromRGB(255, 255, 255),
    DialogInputLine = Color3.fromRGB(200, 200, 200),
    Text = Color3.fromRGB(30, 30, 30),
    SubText = Color3.fromRGB(100, 100, 100),
    Hover = Color3.fromRGB(220, 220, 220),
    HoverChange = 0.05,
    Background = "https://raw.githubusercontent.com/StyearX/Script/main/not%20a%20luau/Background.webm",
    BackgroundTransparency = 0,
    BackgroundImagesRectPosition = nil,
    BackgroundImagesRectSize = nil,
    ViewportBackground = Color3.fromRGB(240, 240, 240),
    ViewportBackgroundImages = true,
    DropdownOutsideWindowBackground = Color3.fromRGB(248, 248, 248),
    DropdownOutsideWindowBackgroundImages = true,
    ShineEnabled = true,
    Shine = {
        Speed = 0.6,
        RotationSpeed = 30,
        ColorSequence = ColorSequence.new({
            ColorSequenceKeypoint.new(0, Color3.fromRGB(80, 180, 255)),
            ColorSequenceKeypoint.new(0.3, Color3.fromRGB(150, 220, 255)),
            ColorSequenceKeypoint.new(0.6, Color3.fromRGB(220, 250, 255)),
            ColorSequenceKeypoint.new(0.8, Color3.fromRGB(150, 220, 255)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(80, 180, 255))
        })
    },
    StrokeShine = true,
    StrokeDark = Color3.fromRGB(180, 180, 180),
    ButtonGradient = {
        Background = ColorSequence.new({
            ColorSequenceKeypoint.new(0, Color3.fromRGB(210, 235, 255)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(170, 210, 250))
        }),
        Stroke = ColorSequence.new({
            ColorSequenceKeypoint.new(0, Color3.fromRGB(70, 130, 255)),
            ColorSequenceKeypoint.new(0.3, Color3.fromRGB(150, 200, 255)),
            ColorSequenceKeypoint.new(0.6, Color3.fromRGB(220, 240, 255)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(70, 130, 255))
        })
    },
    DiscordJoinButton = Color3.fromRGB(88, 101, 242),
    WarningNotifyColor = Color3.fromRGB(255, 185, 30),
    SuccessNotifyColor = Color3.fromRGB(50, 205, 80),
    ErrorNotifyColor = Color3.fromRGB(220, 55, 55),
    InfoNotifyColor = Color3.fromRGB(76, 194, 255)
})

Fluent:RegisterCustomTheme("Azure", {
    Accent = Color3.fromRGB(50, 150, 255),
    AcrylicMain = Color3.fromRGB(20, 38, 65),
    AcrylicBorder = Color3.fromRGB(90, 160, 240),
    AcrylicGradient = ColorSequence.new(Color3.fromRGB(55, 90, 150), Color3.fromRGB(15, 28, 50)),
    AcrylicNoise = 0.90,
    TitleBarLine = Color3.fromRGB(110, 200, 255),
    Tab = Color3.fromRGB(30, 52, 85),
    Element = Color3.fromRGB(45, 75, 120),
    ElementBorder = Color3.fromRGB(130, 200, 255),
    InElementBorder = Color3.fromRGB(100, 170, 240),
    ElementTransparency = 0.92,
    ToggleSlider = Color3.fromRGB(200, 235, 255),
    ToggleToggled = Color3.fromRGB(50, 150, 255),
    SliderRail = Color3.fromRGB(75, 120, 180),
    DropdownFrame = Color3.fromRGB(25, 45, 75),
    DropdownHolder = Color3.fromRGB(18, 32, 60),
    DropdownBorder = Color3.fromRGB(100, 170, 240),
    DropdownOption = Color3.fromRGB(42, 70, 112),
    Keybind = Color3.fromRGB(25, 45, 75),
    Input = Color3.fromRGB(25, 45, 75),
    InputFocused = Color3.fromRGB(52, 85, 135),
    InputIndicator = Color3.fromRGB(85, 180, 255),
    Dialog = Color3.fromRGB(20, 38, 65),
    DialogHolder = Color3.fromRGB(15, 28, 50),
    DialogHolderLine = Color3.fromRGB(110, 190, 255),
    DialogButton = Color3.fromRGB(45, 75, 120),
    DialogButtonBorder = Color3.fromRGB(110, 190, 255),
    DialogBorder = Color3.fromRGB(100, 170, 240),
    DialogInput = Color3.fromRGB(25, 45, 75),
    DialogInputLine = Color3.fromRGB(85, 180, 255),
    Text = Color3.fromRGB(250, 252, 255),
    SubText = Color3.fromRGB(200, 225, 250),
    Hover = Color3.fromRGB(255, 255, 255),
    HoverChange = 0.08,
    Background = "https://raw.githubusercontent.com/StyearX/Script/main/not%20a%20luau/Shimoneta%20-%20Nishikinomiya%20Anna%20Render%201.png",
    BackgroundTransparency = 0,
    ViewportBackground = Color3.fromRGB(15, 28, 50),
    ViewportBackgroundImages = true,
    DropdownOutsideWindowBackground = Color3.fromRGB(18, 32, 60),
    DropdownOutsideWindowBackgroundImages = true,
    ShineEnabled = true,
    Shine = {
        Speed = 0.35,
        RotationSpeed = 15,
        ColorSequence = ColorSequence.new({
            ColorSequenceKeypoint.new(0, Color3.fromRGB(50, 150, 255)),
            ColorSequenceKeypoint.new(0.2, Color3.fromRGB(100, 200, 255)),
            ColorSequenceKeypoint.new(0.4, Color3.fromRGB(160, 230, 255)),
            ColorSequenceKeypoint.new(0.6, Color3.fromRGB(220, 250, 255)),
            ColorSequenceKeypoint.new(0.8, Color3.fromRGB(180, 220, 255)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(50, 150, 255))
        })
    },
    StrokeShine = true,
    StrokeDark = Color3.fromRGB(65, 110, 170),
    ButtonGradient = {
        Background = ColorSequence.new({
            ColorSequenceKeypoint.new(0, Color3.fromRGB(60, 160, 255)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(30, 85, 180))
        }),
        Stroke = ColorSequence.new({
            ColorSequenceKeypoint.new(0, Color3.fromRGB(180, 235, 255)),
            ColorSequenceKeypoint.new(0.5, Color3.fromRGB(110, 200, 255)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(100, 170, 240))
        })
    }
})

Fluent:RegisterCustomTheme("Scarlet", {
    Accent = Color3.fromRGB(255, 50, 80),
    AcrylicMain = Color3.fromRGB(45, 18, 28),
    AcrylicBorder = Color3.fromRGB(220, 70, 100),
    AcrylicGradient = ColorSequence.new(Color3.fromRGB(75, 30, 45), Color3.fromRGB(30, 10, 18)),
    AcrylicNoise = 0.90,
    TitleBarLine = Color3.fromRGB(255, 80, 120),
    Tab = Color3.fromRGB(55, 22, 35),
    Element = Color3.fromRGB(60, 28, 42),
    ElementBorder = Color3.fromRGB(255, 90, 130),
    InElementBorder = Color3.fromRGB(200, 80, 115),
    ElementTransparency = 0.92,
    ToggleSlider = Color3.fromRGB(255, 210, 225),
    ToggleToggled = Color3.fromRGB(255, 60, 95),
    SliderRail = Color3.fromRGB(120, 50, 70),
    DropdownFrame = Color3.fromRGB(50, 20, 32),
    DropdownHolder = Color3.fromRGB(38, 14, 24),
    DropdownBorder = Color3.fromRGB(190, 75, 110),
    DropdownOption = Color3.fromRGB(58, 26, 40),
    Keybind = Color3.fromRGB(50, 20, 32),
    Input = Color3.fromRGB(50, 20, 32),
    InputFocused = Color3.fromRGB(72, 35, 52),
    InputIndicator = Color3.fromRGB(255, 75, 110),
    Dialog = Color3.fromRGB(45, 18, 28),
    DialogHolder = Color3.fromRGB(35, 12, 22),
    DialogHolderLine = Color3.fromRGB(210, 80, 120),
    DialogButton = Color3.fromRGB(60, 28, 42),
    DialogButtonBorder = Color3.fromRGB(210, 80, 120),
    DialogBorder = Color3.fromRGB(190, 75, 110),
    DialogInput = Color3.fromRGB(50, 20, 32),
    DialogInputLine = Color3.fromRGB(255, 75, 110),
    Text = Color3.fromRGB(255, 245, 250),
    SubText = Color3.fromRGB(235, 200, 215),
    Hover = Color3.fromRGB(255, 255, 255),
    HoverChange = 0.08,
    Background = "rbxassetid://82815674474603",
    BackgroundTransparency = 0.12,
    ViewportBackground = Color3.fromRGB(30, 10, 18),
    ViewportBackgroundImages = true,
    DropdownOutsideWindowBackground = Color3.fromRGB(35, 12, 22),
    DropdownOutsideWindowBackgroundImages = true,
    ShineEnabled = true,
    Shine = {
        Speed = 0.35,
        RotationSpeed = 15,
        ColorSequence = ColorSequence.new({
            ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 50, 80)),
            ColorSequenceKeypoint.new(0.2, Color3.fromRGB(255, 120, 100)),
            ColorSequenceKeypoint.new(0.4, Color3.fromRGB(255, 200, 140)),
            ColorSequenceKeypoint.new(0.6, Color3.fromRGB(255, 255, 200)),
            ColorSequenceKeypoint.new(0.8, Color3.fromRGB(255, 160, 180)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 50, 80))
        })
    },
    StrokeShine = true,
    StrokeDark = Color3.fromRGB(110, 40, 60),
    ButtonGradient = {
        Background = ColorSequence.new({
            ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 60, 95)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(140, 30, 55))
        }),
        Stroke = ColorSequence.new({
            ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 200, 225)),
            ColorSequenceKeypoint.new(0.5, Color3.fromRGB(255, 110, 150)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(210, 80, 120))
        })
    }
})

Fluent:RegisterCustomTheme("Toxic", {
    Accent = Color3.fromRGB(140, 255, 80),
    AcrylicMain = Color3.fromRGB(20, 30, 15),
    AcrylicBorder = Color3.fromRGB(120, 220, 90),
    AcrylicGradient = ColorSequence.new(Color3.fromRGB(45, 75, 30), Color3.fromRGB(12, 20, 10)),
    AcrylicNoise = 0.90,
    TitleBarLine = Color3.fromRGB(160, 255, 110),
    Tab = Color3.fromRGB(28, 45, 22),
    Element = Color3.fromRGB(38, 60, 30),
    ElementBorder = Color3.fromRGB(150, 240, 100),
    InElementBorder = Color3.fromRGB(120, 210, 90),
    ElementTransparency = 0.92,
    ToggleSlider = Color3.fromRGB(220, 255, 200),
    ToggleToggled = Color3.fromRGB(140, 255, 80),
    SliderRail = Color3.fromRGB(80, 130, 60),
    DropdownFrame = Color3.fromRGB(22, 38, 18),
    DropdownHolder = Color3.fromRGB(16, 28, 13),
    DropdownBorder = Color3.fromRGB(120, 210, 90),
    DropdownOption = Color3.fromRGB(36, 58, 28),
    Keybind = Color3.fromRGB(22, 38, 18),
    Input = Color3.fromRGB(22, 38, 18),
    InputFocused = Color3.fromRGB(44, 68, 35),
    InputIndicator = Color3.fromRGB(150, 240, 100),
    Dialog = Color3.fromRGB(20, 30, 15),
    DialogHolder = Color3.fromRGB(15, 24, 12),
    DialogHolderLine = Color3.fromRGB(160, 250, 110),
    DialogButton = Color3.fromRGB(38, 60, 30),
    DialogButtonBorder = Color3.fromRGB(160, 250, 110),
    DialogBorder = Color3.fromRGB(120, 210, 90),
    DialogInput = Color3.fromRGB(22, 38, 18),
    DialogInputLine = Color3.fromRGB(150, 240, 100),
    Text = Color3.fromRGB(240, 255, 235),
    SubText = Color3.fromRGB(205, 235, 195),
    Hover = Color3.fromRGB(255, 255, 255),
    HoverChange = 0.08,
    Background = "rbxassetid://91484259372386",
    BackgroundTransparency = 0.12,
    ViewportBackground = Color3.fromRGB(15, 24, 12),
    ViewportBackgroundImages = true,
    DropdownOutsideWindowBackground = Color3.fromRGB(16, 28, 13),
    DropdownOutsideWindowBackgroundImages = true,
    ShineEnabled = true,
    Shine = {
        Speed = 0.35,
        RotationSpeed = 15,
        ColorSequence = ColorSequence.new({
            ColorSequenceKeypoint.new(0, Color3.fromRGB(140, 255, 80)),
            ColorSequenceKeypoint.new(0.2, Color3.fromRGB(180, 255, 120)),
            ColorSequenceKeypoint.new(0.4, Color3.fromRGB(220, 255, 170)),
            ColorSequenceKeypoint.new(0.6, Color3.fromRGB(255, 255, 220)),
            ColorSequenceKeypoint.new(0.8, Color3.fromRGB(200, 255, 150)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(140, 255, 80))
        })
    },
    StrokeShine = true,
    StrokeDark = Color3.fromRGB(70, 120, 50),
    ButtonGradient = {
        Background = ColorSequence.new({
            ColorSequenceKeypoint.new(0, Color3.fromRGB(150, 255, 90)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(60, 150, 30))
        }),
        Stroke = ColorSequence.new({
            ColorSequenceKeypoint.new(0, Color3.fromRGB(210, 255, 180)),
            ColorSequenceKeypoint.new(0.5, Color3.fromRGB(150, 240, 100)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(120, 210, 90))
        })
    }
})

Window = Fluent:CreateWindow({
    Title = "GoonWares",
    SubTitle = "Made by: StyearX",
    TabWidth = isMobile and 130 or 150,
    Tags = {
        { Text = ExecutorName, Color = Color3.fromRGB(200, 200, 200) },
        { Text = "Hello " .. tostring(LocalPlayer.DisplayName), Color = Color3.fromRGB(211, 20, 10) }
        { Text = "Currently Beta", Color = Color3.fromRGB(200, 21, 78) },
    },
    Version = "Arsenal",
    Acrylic = true,
    Size = isMobile and UDim2.fromOffset(500, 500) or UDim2.fromOffset(580, 580),
    Theme = "Crimson",
    Icons = "rbxassetid://139095000385640",
    TitleIcon = "rbxassetid://139379979502671",
    Font = "GothamSSm", 
    UserInfoTop = true,
    UserInfoTitle = LocalPlayer.Name,
    UserInfoSubtitle = LocalPlayer.DisplayName,
    Anonymous = {                 
        Default = false,        
        ShowAno = true,         
        AnoUserInfoTitle = "hides",
        AnoUserInfoSubTitle = "hides",
        Icons = "rbxassetid://139095000385640", 
    },
    MinimizeKey = Enum.KeyCode.G,
    FolderName   = "GoonWares",  
    ScreenGuiName = "Arsenal",
})

Tabs = {
    Main = Window:AddTab({ Title = "|  Main", Icon = "lucide/layout-dashboard" }),
    Misc = Window:AddTab({ Title = "|  Misc", Icon = "rbxassetid://7733960981" }),
    AutoFarm = Window:AddTab({ Title = "|  Auto Farm", Icon = "rbxassetid://10709811110" }),
    Combat = Window:AddTab({ Title = "|  Combat", Icon = "bomb" }),
    Visual = Window:AddTab({ Title = "|  Visual", Icon = "rbxassetid://10709819149" }),
    Info = Window:AddTab({ Title = "|  Info", Icon = "rbxassetid://10723415903" }),
    Settings = Window:AddTab({ Title = "|  Settings", Icon = "rbxassetid://7734052335", Favoriteable = true }),
    Extension = Window:AddTab({ Title = "|  Extension", Icon = "rbxassetid://10734930886" }),
}

task.spawn(function()
    local hasAccepted = false
    local dialogResult = nil
    
    Window:Dialog({
        Title = "accept pls",
        Content = "Accept nga",
        Buttons = {
            {
                Title = "Accept",
                Callback = function()
                    dialogResult = "accept"
                    hasAccepted = true
                    
                    local success, err = pcall(function()
                        local TextChatService = game:GetService("TextChatService")
                        local message = "As someone with skill issues I would use exploits "
                        
                        for _, channel in pairs(TextChatService.TextChannels:GetChildren()) do
                            if channel.Name ~= "RBXSystem" then
                                channel:SendAsync(message)
                            end
                        end
                    end)
                    
                    if success then
                        Fluent:Notify({
                            Title = "the loser accept ",
                            Content = "Traps buddy!",
                            Duration = 3
                        })
                    else
                        Fluent:Notify({
                            Title = "failed",
                            Content = "Failed to send message: " .. tostring(err),
                            Duration = 3
                        })
                    end
                end
            },
            {
                Title = "No",
                Callback = function()
                    dialogResult = "deny"
                    hasAccepted = true
                    Fluent:Notify({
                        Title = "the loser refused ",
                        Content = "Alright,",
                        Duration = 2
                    })
                end
            }
        }
    })
    
    local timeout = 300
    local startTime = tick()
    
    while not hasAccepted and tick() - startTime < timeout do
        task.wait(0.1)
    end
    
    if not hasAccepted then
        Fluent:Notify({
            Title = "timeout",
            Content = "No response received, skipping traps.",
            Duration = 2
        })
    end
end)

function UniverseServerTools(Tabs)
    HttpService = game:GetService("HttpService")
    TeleportService = game:GetService("TeleportService")
    Players = game:GetService("Players")
    MarketplaceService = game:GetService("MarketplaceService")
    Stats = game:GetService("Stats")
    RunService = game:GetService("RunService")
    Workspace = game:GetService("Workspace")
    UserInputService = game:GetService("UserInputService")
    jobId = game.JobId
    placeId = game.PlaceId
    LocalPlayer = Players.LocalPlayer
    StartTime = tick()

    ENGINE_TARGET_MB = 3000
    FrameCount = 0
    LastTime = tick()
    CurrentFPS = 0

    RunService.RenderStepped:Connect(function()
        FrameCount = FrameCount + 1
        local CurrentTime = tick()
        if CurrentTime - LastTime >= 1 then
            CurrentFPS = FrameCount
            FrameCount = 0
            LastTime = CurrentTime
        end
    end)

    function format(Int)
        return string.format("%02i", Int)
    end

    function convertToDetailedTime(Seconds, Milliseconds)
        local years = math.floor(Seconds / 31536000)
        Seconds = Seconds % 31536000
        
        local months = math.floor(Seconds / 2592000)
        Seconds = Seconds % 2592000
        
        local weeks = math.floor(Seconds / 604800)
        Seconds = Seconds % 604800
        
        local days = math.floor(Seconds / 86400)
        Seconds = Seconds % 86400
        
        local hours = math.floor(Seconds / 3600)
        Seconds = Seconds % 3600
        
        local minutes = math.floor(Seconds / 60)
        local seconds = Seconds % 60
        
        return {
            Years = years,
            Months = months,
            Weeks = weeks,
            Days = days,
            Hours = hours,
            Minutes = minutes,
            Seconds = seconds,
            Milliseconds = Milliseconds or 0
        }
    end

    function GetServerUptimeString()
        local totalSeconds = time()
        local milliseconds = math.floor((totalSeconds - math.floor(totalSeconds)) * 1000)
        local uptime = convertToDetailedTime(math.floor(totalSeconds), milliseconds)
        return string.format(
            "%dY %dMth %dW %dD %dH %dMin %dSec %dMS",
            uptime.Years, uptime.Months, uptime.Weeks, uptime.Days, 
            uptime.Hours, uptime.Minutes, uptime.Seconds, uptime.Milliseconds
        )
    end

    function GetLastJoinedString()
        local currentTimeSeconds = os.time()
        local joinedTimeSeconds = currentTimeSeconds - math.floor(time())
        local joinedTime = os.date("*t", joinedTimeSeconds)
        
        local hour = joinedTime.hour
        local minute = joinedTime.min
        local second = joinedTime.sec
        
        local ampm = hour >= 12 and "PM" or "AM"
        local hour12 = hour % 12
        if hour12 == 0 then hour12 = 12 end
        
        local millisecond = math.floor((time() - math.floor(time())) * 1000)
        
        local monthNames = { "Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec" }
        local monthName = monthNames[joinedTime.month]
        
        return string.format("%02d:%02d:%02d.%03d %s - %s %d, %d",
            hour12, minute, second, millisecond, ampm, monthName, joinedTime.day, joinedTime.year)
    end

    function GetScriptRuntime()
        local elapsed = tick() - StartTime
        local milliseconds = math.floor((elapsed - math.floor(elapsed)) * 1000)
        local runtime = convertToDetailedTime(math.floor(elapsed), milliseconds)
        return string.format(
            "%dY %dMth %dW %dD %dH %dMin %dSec %dMS",
            runtime.Years, runtime.Months, runtime.Weeks, runtime.Days, 
            runtime.Hours, runtime.Minutes, runtime.Seconds, runtime.Milliseconds
        )
    end

    function GetExecutedSince()
        local executedTime = os.date("%I:%M:%S %p", StartTime)
        return executedTime
    end

    function GetOSClock()
        local now = os.date("*t")
        local hour = now.hour
        local minute = now.min
        local second = now.sec
        local ampm = hour >= 12 and "PM" or "AM"
        local hour12 = hour % 12
        if hour12 == 0 then hour12 = 12 end
        return string.format("%02d:%02d:%02d %s", hour12, minute, second, ampm)
    end

    function GetCalendarDate()
        local now = os.date("*t")
        local monthNames = { "January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December" }
        local suffix = "th"
        if now.day == 1 or now.day == 21 or now.day == 31 then suffix = "st"
        elseif now.day == 2 or now.day == 22 then suffix = "nd"
        elseif now.day == 3 or now.day == 23 then suffix = "rd" end
        return string.format("%s %d%s, %d", monthNames[now.month], now.day, suffix, now.year)
    end

    function GetGPUInfo()
        local gpuTime = Stats.RenderGPUFrameTime
        local gpuMs = string.format("%.2f", gpuTime * 1000)
        return string.format("%s ms", gpuMs)
    end

    function GetCPUInfo()
        local cpuTime = Stats.RenderCPUFrameTime
        local cpuMs = string.format("%.2f", cpuTime * 1000)
        return string.format("%s ms", cpuMs)
    end

    function GetRAMInfo()
        local currentRAM = Stats:GetTotalMemoryUsageMb()
        local usagePercent = math.floor((currentRAM / ENGINE_TARGET_MB) * 100)
        return string.format("%.2f MB / %d MB (%d%%)", currentRAM, ENGINE_TARGET_MB, usagePercent)
    end

    function GetNetworkSent()
        local sentRate = Stats.DataSendKbps
        return string.format("%.2f KB/s", sentRate)
    end

    function GetNetworkReceived()
        local receivedRate = Stats.DataReceiveKbps
        return string.format("%.2f KB/s", receivedRate)
    end

    function GetPing()
        local ping = math.clamp(Stats.Network.ServerStatsItem["Data Ping"]:GetValue(), 10, 700)
        return string.format("%d ms", math.floor(ping))
    end

    function GetFPS()
        return string.format("%d fps", CurrentFPS)
    end

    function LaunchID()
        return string.format("roblox://placeId=%d&gameInstanceId=%s", placeId, jobId)
    end

    function getServerLink()
        return string.format("darahub.pages.dev/roblox-launch.html?placeId=%d&gameInstanceId=%s", placeId, jobId)
    end  -- FIXED: typo `en` -> `end`

    function rejoinServer()
        local success, err = pcall(function()
            local currentJobId = game.JobId
            local cursor = ""
            local bool = false
            
            repeat
                local url = "https://games.roblox.com/v1/games/" .. game.PlaceId .. "/servers/Public?limit=100"
                if cursor ~= "" then
                    url = url .. "&cursor=" .. cursor
                end
                
                local success, result = pcall(function()
                    return game:HttpGet(url)
                end)
                
                if not success then
                    return
                end
                
                local data = HttpService:JSONDecode(result)
                
                if data and data.data then
                    for _, server in ipairs(data.data) do
                        if server.id == currentJobId then
                            bool = true
                            TeleportService:TeleportToPlaceInstance(game.PlaceId, game.JobId, Players.LocalPlayer)
                            return
                        end
                    end
                end
                
                cursor = data and data.nextPageCursor or ""
            until cursor == ""
            
            if not bool then
                TeleportService:Teleport(game.PlaceId)
            end
        end)
        
        if not success then
            Fluent:Notify({ Title = "Rejoin Error", Content = "Failed to rejoin: " .. tostring(err), Duration = 3 })
        end
    end

    TotalFriends = 0
    OnlineFriends = 0
    OfflineFriends = 0

    function UpdateFriendData()
        pcall(function()
            local total = 0
            local online = 0
            
            local friendsList = Players:GetFriendsAsync(LocalPlayer.UserId)
            while true do
                for _, data in friendsList:GetCurrentPage() do
                    total = total + 1
                end
                if friendsList.IsFinished then
                    break
                else
                    friendsList:AdvanceToNextPageAsync()
                end
            end
            
            local onlineFriendsData = LocalPlayer:GetFriendsOnline()
            for _ in pairs(onlineFriendsData) do
                online = online + 1
            end
            
            TotalFriends = total
            OnlineFriends = online
            OfflineFriends = total - online
        end)
    end

    task.spawn(UpdateFriendData)

    task.spawn(function()
        while true do
            task.wait(30)
            UpdateFriendData()
        end
    end)

    local SecServerInfo = Tabs.Misc:AddSection("Server Information", "solar/widget-2-bold")
    SecServerInfo:AddDivider()

    ServerInfoParagraph = SecServerInfo:AddParagraph({
        Title = "In Server For",
        Content = GetServerUptimeString()
    })

    task.spawn(function()
        while true do task.wait()
            pcall(function()
                ServerInfoParagraph:SetDesc(GetServerUptimeString())
            end)
        end
    end)

    SecServerInfo:AddParagraph({
        Title = "Server Started",
        Content = GetLastJoinedString()
    })

    placeName = "Unknown"
    pcall(function()
        local productInfo = MarketplaceService:GetProductInfo(placeId)
        if productInfo and productInfo.Name then
            placeName = productInfo.Name
        end
    end)

    SecServerInfo:AddParagraph({
        Title = "Game",
        Content = placeName
    })

    numPlayers = #Players:GetPlayers()
    maxPlayers = Players.MaxPlayers
    CurrentPlayersParagraph = SecServerInfo:AddParagraph({
        Title = "Current Players",
        Content = numPlayers .. " / " .. maxPlayers
    })

    task.spawn(function()
        while true do task.wait()
            pcall(function()
                CurrentPlayersParagraph:SetDesc(#Players:GetPlayers() .. " / " .. maxPlayers)
            end)
        end
    end)

    SecServerInfo:AddParagraph({
        Title = "Server ID",
        Content = string.sub(jobId, 1, 30) .. "..."
    })


    Tabs.Misc:AddSpace({ Height = 20 })
    local SecClientInfo = Tabs.Misc:AddSection("Client Information", "solar/widget-2-bold")
    SecClientInfo:AddDivider()

    ScriptRuntimeParagraph = SecClientInfo:AddParagraph({
        Title = "Script Running For",
        Content = GetScriptRuntime()
    })

    task.spawn(function()
        while true do task.wait()
            pcall(function()
                ScriptRuntimeParagraph:SetDesc(GetScriptRuntime())
            end)
        end
    end)

    SecClientInfo:AddParagraph({
        Title = "Executed Since",
        Content = GetExecutedSince()
    })


    Tabs.Misc:AddSpace({ Height = 20 })
    local SecSystemInfo = Tabs.Misc:AddSection("System Information", "solar/widget-2-bold")
    SecSystemInfo:AddDivider()

    OSClockParagraph = SecSystemInfo:AddParagraph({
        Title = "OS Clock",
        Content = GetOSClock()
    })

    task.spawn(function()
        while true do task.wait()
            pcall(function()
                OSClockParagraph:SetDesc(GetOSClock())
            end)
        end
    end)

    CalendarParagraph = SecSystemInfo:AddParagraph({
        Title = "Calendar",
        Content = GetCalendarDate()
    })

    task.spawn(function()
        while true do
            task.wait(60)
            pcall(function()
                CalendarParagraph:SetDesc(GetCalendarDate())
            end)
        end
    end)

    GPUParagraph = SecSystemInfo:AddParagraph({
        Title = "GPU",
        Content = GetGPUInfo()
    })

    CPUParagraph = SecSystemInfo:AddParagraph({
        Title = "CPU",
        Content = GetCPUInfo()
    })

    RAMParagraph = SecSystemInfo:AddParagraph({
        Title = "RAM",
        Content = GetRAMInfo()
    })

    SentParagraph = SecSystemInfo:AddParagraph({
        Title = "Sent",
        Content = GetNetworkSent()
    })

    ReceivedParagraph = SecSystemInfo:AddParagraph({
        Title = "Received",
        Content = GetNetworkReceived()
    })

    PingParagraph = SecSystemInfo:AddParagraph({
        Title = "Ping",
        Content = GetPing()
    })

    FPSParagraph = SecSystemInfo:AddParagraph({
        Title = "FPS",
        Content = GetFPS()
    })

    task.spawn(function()
        while true do
            task.wait(0.2)
            pcall(function()
                GPUParagraph:SetDesc(GetGPUInfo())
                CPUParagraph:SetDesc(GetCPUInfo())
                RAMParagraph:SetDesc(GetRAMInfo())
                SentParagraph:SetDesc(GetNetworkSent())
                ReceivedParagraph:SetDesc(GetNetworkReceived())
                PingParagraph:SetDesc(GetPing())
                FPSParagraph:SetDesc(GetFPS())
            end)
        end
    end)


    Tabs.Misc:AddSpace({ Height = 20 })
    local SecPlayerInfo = Tabs.Misc:AddSection("Player Information", "solar/widget-2-bold")
    SecPlayerInfo:AddDivider()

    SecPlayerInfo:AddParagraph({
        Title = "Username",
        Content = LocalPlayer.Name
    })

    SecPlayerInfo:AddParagraph({
        Title = "Display Name",
        Content = LocalPlayer.DisplayName
    })

    SecPlayerInfo:AddParagraph({
        Title = "User ID",
        Content = tostring(LocalPlayer.UserId)
    })

    accountCreationString = "Unknown"
    pcall(function()
        local accountAge = LocalPlayer.AccountAge
        if accountAge then
            local creationTime = os.time() - (accountAge * 86400)
            accountCreationString = os.date("%B %d, %Y", creationTime)
        end
    end)

    SecPlayerInfo:AddParagraph({
        Title = "Account Created",
        Content = accountCreationString
    })


    Tabs.Misc:AddSpace({ Height = 20 })
    local SecFriendsData = Tabs.Misc:AddSection("Friends Data", "solar/widget-2-bold")
    SecFriendsData:AddDivider()

    FriendsOnlineParagraph = SecFriendsData:AddParagraph({
        Title = "Online Friends",
        Content = "0"
    })

    FriendsOfflineParagraph = SecFriendsData:AddParagraph({
        Title = "Offline Friends",
        Content = "0"
    })

    FriendsTotalParagraph = SecFriendsData:AddParagraph({
        Title = "Total Friends",
        Content = "0"
    })

    task.spawn(function()
        while true do
            task.wait(30)
            pcall(function()
                UpdateFriendData()
                FriendsOnlineParagraph:SetDesc(tostring(OnlineFriends))
                FriendsOfflineParagraph:SetDesc(tostring(OfflineFriends))
                FriendsTotalParagraph:SetDesc(tostring(TotalFriends))
            end)
        end
    end)

    pcall(function()
        UpdateFriendData()
        FriendsOnlineParagraph:SetDesc(tostring(OnlineFriends))
        FriendsOfflineParagraph:SetDesc(tostring(OfflineFriends))
        FriendsTotalParagraph:SetDesc(tostring(TotalFriends))
    end)


    Tabs.Misc:AddSpace({ Height = 20 })
    local SecServerTools = Tabs.Misc:AddSection("Server Tools", "solar/widget-2-bold")
    SecServerTools:AddDivider()

    SecServerTools:AddButton({
        Title = "Rejoin",
        Description = "Rejoin the current server",
        Icon = "refresh-cw",
        Callback = function()
            Window:Dialog({
                Title = "Rejoin",
                Content = "Are you sure you want to rejoin the current server?",
                Buttons = {
                    { Title = "Yes", Callback = function() rejoinServer() end },
                    { Title = "No" },
                },
            })
        end
    })

    SecServerTools:AddButton({
        Title = "Copy Server Launch ID",
        Description = "Copy the current server's Launch ID",
        Icon = "link",
        Callback = function()
            pcall(function()
                if setclipboard then setclipboard(LaunchID()) end
            end)
            Fluent:Notify({ Icon = "link", Title = "Copied", Content = "Server Launch ID copied", Duration = 2 })
        end
    })

    SecServerTools:AddButton({
        Title = "Copy Server Link",
        Description = "Copy the current server's join link",
        Icon = "link",
        Callback = function()
            pcall(function()
                if setclipboard then setclipboard(getServerLink()) end
            end)
            Fluent:Notify({ Icon = "link", Title = "Copied", Content = "Server link copied", Duration = 2 })
        end
    })

end
UniverseServerTools(Tabs)

DConfiguration = {
    Combat = {
        Aimbot = false,
        SilentAim = false,
        SilentAimConnection = nil,
        TeamCheck = false,
        AimPart = "Head",
        FovSize = 150,
        Smoothness = 1,
        MaxDistance = 1000,
        WallCheck = false,
        RequireKeyHold = false,
        ShowFOV = false,
        BlacklistedTeams = {},
        FovColor = Color3.fromRGB(255, 220, 0),
        FovRGB = false,
        FovLocked = true,
    },
    Weapon = {
        InfAmmo = false,
        FireRate = false,
        NoRecoil = false,
        WeaponConnections = {}
    },
    Player = {
        SpeedHack = false,
        InfiniteJump = false,
        WalkSpeed = 16,
        InfiniteJumpConnection = nil,
        SpeedHackConnection = nil,
        CharacterAddedConnection = nil,
    },
    Esp = {
        Enabled = false,
        TeamCheck = true,
        BoxType = "2D",
        BoxesEnabled = true,
        NamesEnabled = true,
        DistanceEnabled = true,
        HighlightsEnabled = false,
        TracerEnabled = false,
        TracerPosition = "Bottom",
        TracerThickness = 1,
        Elements = {},
        RenderConnection = nil
    },
    Fov = {
        Enabled = false,
        Radius = 150,
        Color = Color3.fromRGB(255, 255, 255),
        Thickness = 2,
        LockToCenter = true,
        Transparency = 0.3,
        Filled = false,
        Sides = 64
    },
    Forcefield = {
        ApplyEnabled = false,
        IncludeNpc = false,
        Selected = {},
        Color = Color3.fromRGB(0, 170, 255),
        UseRGB = false,
        RGBSpeed = 1,
        Connections = {},
        DiedConn = {},
        CharAddedConn = {},
        Originals = {},
        NpcCharacters = {},
    },
    Arms = {
        Connection = nil,
        Originals = {},
    },
    Gun = {
        Enabled = false,
        Color = Color3.fromRGB(0, 170, 255),
        UseRGB = false,
        RGBSpeed = 1,
        Connection = nil,
        Originals = {},
    },
    RGBGun = {
        Enabled = false,
        Speed = 1,
        Connection = nil,
        Originals = {},
    },
    ForcefieldEnemy = {
        ApplyEnabled = false,
        Connections = {},
        DiedConn = {},
        CharAddedConn = {},
        Originals = {},
    }
}

local StoragePath = "GoonWares/Arsenal/FFlags.json"

local FFlagHandler = {}

function FFlagHandler:SetFFlag(flag, value)
    if type(flag) ~= "string" or flag:gsub(" ", ""):len() == 0 then
        return false, "InvalidFlagName"
    end

    local stripped = flag
        :gsub("^DFInt", "")
        :gsub("^DFFlag", "")
        :gsub("^FFlag", "")
        :gsub("^FInt", "")
        :gsub("^DFString", "")
        :gsub("^FString", "")

    local strValue
    if type(value) == "boolean" then
        strValue = value and "True" or "False"
    else
        strValue = tostring(value)
    end

    local success = false
    local method = "Unknown"

    local ok = pcall(setfflag, stripped, strValue)
    if ok then
        success = true
        method = "NativeStripped"
    else
        local ok2 = pcall(setfflag, flag, strValue)
        if ok2 then
            success = true
            method = "NativeFull"
        else
            local ok3 = pcall(function()
                if settings() and settings().FFlags then
                    settings().FFlags[flag] = strValue
                end
            end)
            if ok3 then
                success = true
                method = "Settings"
            end
        end
    end

    if success then
        pcall(function()
            local raw = readfile(StoragePath)
            local fflagfile = raw and HttpService:JSONDecode(raw) or {}
            fflagfile[flag] = strValue
            writefile(StoragePath, HttpService:JSONEncode(fflagfile))
        end)
        return true, method
    end

    return false, "InjectionFailed"
end

function FFlagHandler:BulkSet(flagsTable)
    local results = { success = 0, failed = 0, failedFlags = {} }

    for flag, value in pairs(flagsTable) do
        local ok = self:SetFFlag(flag, value)
        if ok then
            results.success = results.success + 1
        else
            results.failed = results.failed + 1
            table.insert(results.failedFlags, flag)
        end
        task.wait(0.05)
    end

    return results
end

function FFlagHandler:ClearFlags()
    pcall(function()
        writefile(StoragePath, "{}")
    end)
    return true
end

local FFlagPresets = {
    LagOptimizer = {
        ["FFlagDebugDisplayFPS"] = true,
        ["FFlagDebugSkyGray"] = true,
        ["FIntRenderShadowIntensity"] = 0,
        ["FFlagGlobalWindRendering"] = false,
        ["FFlagGlobalWindActivated"] = false,
        ["DFFlagDebugPauseVoxelizer"] = true,
        ["DFIntPerformanceControlTextureQualityBestUtility"] = -1,
        ["DFIntMaxFrameBufferSize"] = 4,
        ["FIntRenderLocalLightUpdatesMax"] = 8,
        ["FIntRenderLocalLightUpdatesMin"] = 6,
        ["FFlagDisablePostFx"] = true,
    },
    HighGraphics = {
        ["FIntRomarkStartWithGraphicQualityLevel"] = 21,
        ["DFFlagTextureQualityOverrideEnabled"] = true,
        ["DFIntTextureQualityOverride"] = 3,
        ["FIntDebugForceMSAASamples"] = 4,
        ["FIntRenderShadowmapBias"] = 75,
    },
    UiCleanup = {
        ["FFlagAdServiceEnabled"] = false,
        ["FFlagVoiceBetaBadge"] = false,
        ["FFlagTopBarUseNewBadge"] = false,
        ["FFlagEnableBetaBadgeLearnMore"] = false,
        ["FIntRobloxGuiBlurIntensity"] = 0,
    },
    NetworkTweak = {
        ["FFlagDebugDisableTelemetryEphemeralCounter"] = true,
        ["FFlagDebugDisableTelemetryEphemeralStat"] = true,
        ["FFlagDebugDisableTelemetryEventIngest"] = true,
        ["FFlagDebugDisableTelemetryPoint"] = true,
        ["FFlagDebugDisableTelemetryV2Counter"] = true,
        ["FFlagDebugDisableTelemetryV2Event"] = true,
        ["FFlagDebugDisableTelemetryV2Stat"] = true,
    },
}

local secFastFlags = Tabs.Extension:AddSection("Fastflags Injector", "solar/programming-bold")

secFastFlags:AddParagraph({
    Title = "About Fastflags",
    Content = "Modify internal Roblox client settings. Some changes require a rejoin.",
})

local ffPresetDropdown = secFastFlags:AddDropdown("FF_PresetSelect", {
    ThemedDropdown = true,
    Search = false,
    Title = "Preset",
    Icon = "solar/list-bold",
    Values = { "LagOptimizer", "HighGraphics", "UiCleanup", "NetworkTweak" },
    Default = "LagOptimizer",
    Description = "Preconfigured sets of flags for different purposes.",
    Callback = function(v) end,
})

secFastFlags:AddButton({
    Title = "Load Preset",
    Icon = "solar/download-minimalistic-bold",
    Callback = function()
        local selected = ffPresetDropdown.Value
        local flags = FFlagPresets[selected]

        if not flags then
            Notify("Fastflags", "No preset selected", "Warning")
            return
        end

        local results = FFlagHandler:BulkSet(flags)
        Notify("Fastflags", string.format("%s loaded: %d success, %d failed", selected, results.success, results.failed), "Success")
    end,
})

secFastFlags:AddDivider()

local ffNameInput = secFastFlags:AddInput("FF_FlagName", {
    Title = "Flag Name",
    Icon = "solar/pen-bold",
    Placeholder = "fastflag (String)",
    Default = "",
    Callback = function(v) end,
})

local ffValueInput = secFastFlags:AddInput("FF_FlagValue", {
    Title = "Flag Value",
    Icon = "solar/pen-2-bold",
    Placeholder = "number/boolean",
    Default = "",
    Callback = function(v) end,
})

secFastFlags:AddParagraph({
    Title = "<b>Single Flag Injection</b>",
    Content = "Enter the flag name and its value, then click Inject Flag.",
})

secFastFlags:AddButton({
    Title = "Inject Flag",
    Icon = "solar/syringe-bold",
    Callback = function()
        local flag = ffNameInput.Value
        local rawValue = ffValueInput.Value

        if not flag or flag == "" then
            Notify("Fastflags", "Please enter a flag name", "Warning")
            return
        end

        local parsedValue
        if rawValue:lower() == "true" then
            parsedValue = true
        elseif rawValue:lower() == "false" then
            parsedValue = false
        elseif tonumber(rawValue) then
            parsedValue = tonumber(rawValue)
        else
            parsedValue = rawValue
        end

        local ok, method = FFlagHandler:SetFFlag(flag, parsedValue)
        if ok then
            Notify("Fastflags", string.format("%s = %s (%s)", flag, tostring(parsedValue), method), "Success")
        else
            Notify("Fastflags", string.format("Failed to inject %s. Your executor may not expose setfflag.", flag), "Error")
        end
    end,
})

secFastFlags:AddDivider()

local ffJsonInput = secFastFlags:AddInput("FF_JsonFlags", {
    Title = "Json Fastflag",
    Icon = "solar/code-bold",
    Placeholder = '{ "FFlagDebugDisplayFPS": "True", "FFlagDebugSkyGray": "True" }',
    Default = "",
    Callback = function(v) end,
})

secFastFlags:AddParagraph({
    Title = "<b>Bulk Json Injection</b>",
    Content = "Paste a JSON object with multiple flags, then click Inject Json Flags.",
})

secFastFlags:AddButton({
    Title = "Inject Json Flags",
    Icon = "solar/code-2-bold",
    Callback = function()
        local json = ffJsonInput.Value

        if not json or json == "" then
            Notify("Fastflags", "Please enter JSON data", "Warning")
            return
        end

        local ok, flags = pcall(function()
            return HttpService:JSONDecode(json)
        end)

        if not ok or type(flags) ~= "table" then
            Notify("Fastflags", "Invalid JSON syntax", "Error")
            return
        end

        local results = FFlagHandler:BulkSet(flags)
        Notify("Fastflags", string.format("Json inject: %d success, %d failed", results.success, results.failed), "Success")
    end,
})

secFastFlags:AddDivider()

secFastFlags:AddButton({
    Title = "Clear Injected Flags",
    Icon = "solar/trash-bin-trash-bold",
    Callback = function()
        FFlagHandler:ClearFlags()
        Notify("Fastflags", "All saved flags cleared", "Info")
    end,
})

secFastFlags:AddButton({
    Title = "Rejoin To Fully Apply Fastflags",
    Icon = "solar/restart-bold",
    Description = "Rejoins the game to fully apply all flags.",
    Callback = function()
        Window:Dialog({
            Title = "Rejoin Required",
            Content = "Some flags only take full effect after rejoining. Rejoin now?",
            Buttons = {
                {
                    Title = "Rejoin",
                    Callback = function()
                        Notify("Fastflags", "Rejoining...", "Info")
                        task.wait(0.5)
                        TeleportService:Teleport(game.PlaceId, LocalPlayer)
                    end,
                },
                { Title = "Cancel" },
            },
        })
    end,
})

local AimbotConn = nil
local FovGui = nil
local FovHolder = nil
local FovRing = nil
local FovStroke = nil
local FovRgbHue = 0

local function GetFovCenter()
    if FovHolder and FovHolder.Parent then
        local Pos = FovHolder.AbsolutePosition
        local Sz  = FovHolder.AbsoluteSize
        return Vector2.new(Pos.X + Sz.X / 2, Pos.Y + Sz.Y / 2)
    end
    local Vp = Camera.ViewportSize
    return Vector2.new(Vp.X / 2, Vp.Y / 2)
end

local function GetAimPartFromTarget(Target)
    local Found = Target:FindFirstChild(DConfiguration.Combat.AimPart)
    if Found then return Found end
    return Target:FindFirstChild("HumanoidRootPart") or Target:FindFirstChild("Torso")
end

local function GetClosestTarget()
    local Chr = LocalPlayer.Character
    if not Chr then return nil end
    local LocalRoot = Chr:FindFirstChild("HumanoidRootPart")
    if not LocalRoot then return nil end
    local Nearest, Shortest = nil, math.huge
    local Center = GetFovCenter()

    local function Check(Target)
        if not Target or not Target:IsA("Model") then return end
        local Hum = Target:FindFirstChildOfClass("Humanoid")
        if not Hum or Hum.Health <= 0 then return end
        local Part = GetAimPartFromTarget(Target)
        if not Part then return end
        local Sp, OnScreen = Camera:WorldToScreenPoint(Part.Position)
        if not OnScreen or Sp.Z <= 0 then return end
        local Dist = (Part.Position - LocalRoot.Position).Magnitude
        if Dist > DConfiguration.Combat.MaxDistance then return end
        if (Vector2.new(Sp.X, Sp.Y) - Center).Magnitude > DConfiguration.Combat.FovSize then return end
        if DConfiguration.Combat.WallCheck then
            local Params = RaycastParams.new()
            Params.FilterDescendantsInstances = {Chr}
            Params.FilterType = Enum.RaycastFilterType.Exclude
            local Dir = (Part.Position - Camera.CFrame.Position).Unit * 2000
            local Result = workspace:Raycast(Camera.CFrame.Position, Dir, Params)
            if not Result or not Result.Instance:IsDescendantOf(Target) then return end
        end
        if Dist < Shortest then Shortest = Dist; Nearest = Target end
    end

    local LocalTeam = LocalPlayer.Team
    for _, Player in pairs(Players:GetPlayers()) do
        if Player ~= LocalPlayer then
            local SameTeam = DConfiguration.Combat.TeamCheck
                and LocalTeam ~= nil
                and Player.Team ~= nil
                and Player.Team == LocalTeam
            local Blacklisted = Player.Team ~= nil
                and DConfiguration.Combat.BlacklistedTeams[Player.Team.Name] == true
            if not SameTeam and not Blacklisted then
                pcall(Check, Player.Character)
            end
        end
    end
    return Nearest
end

local function StartFovCircle()
    if FovHolder then FovHolder.Visible = true return end

    FovGui = Instance.new("ScreenGui")
    FovGui.Name = "GWFovGui"
    FovGui.ResetOnSpawn = false
    FovGui.IgnoreGuiInset = true
    FovGui.DisplayOrder = 998
    FovGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    FovGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

    local r = DConfiguration.Combat.FovSize
    FovHolder = Instance.new("Frame")
    FovHolder.BackgroundTransparency = 1
    FovHolder.AnchorPoint = Vector2.new(0.5, 0.5)
    FovHolder.Size = UDim2.fromOffset(r * 2, r * 2)
    FovHolder.Position = UDim2.fromScale(0.5, 0.5)
    FovHolder.ZIndex = 100
    FovHolder.Parent = FovGui

    FovRing = Instance.new("Frame")
    FovRing.BackgroundTransparency = 0.88
    FovRing.BackgroundColor3 = DConfiguration.Combat.FovColor
    FovRing.AnchorPoint = Vector2.new(0.5, 0.5)
    FovRing.Size = UDim2.fromScale(1, 1)
    FovRing.Position = UDim2.fromScale(0.5, 0.5)
    FovRing.ZIndex = 100
    FovRing.Parent = FovHolder
    Instance.new("UICorner", FovRing).CornerRadius = UDim.new(1, 0)

    FovStroke = Instance.new("UIStroke")
    FovStroke.Thickness = 2
    FovStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    FovStroke.Color = DConfiguration.Combat.FovColor
    FovStroke.Parent = FovRing

    -- RGB cycling (runs while FovGui exists, smooth 60fps)
    task.spawn(function()
        while FovGui and FovGui.Parent do
            if DConfiguration.Combat.FovRGB then
                FovRgbHue = (FovRgbHue + 0.5) % 360
                local c = Color3.fromHSV(FovRgbHue / 360, 1, 1)
                FovRing.BackgroundColor3 = c
                FovStroke.Color = c
            end
            task.wait(0.016)
        end
    end)

    -- Drag (same pattern as FloatingButton/OpenShit — FovHolder as target)
    local Dragging, DragInput, DragStart, StartPos = false, nil, nil, nil
    FovHolder.InputBegan:Connect(function(Input)
        if Input.UserInputType ~= Enum.UserInputType.MouseButton1
        and Input.UserInputType ~= Enum.UserInputType.Touch then return end
        if DConfiguration.Combat.FovLocked then return end
        Dragging = true
        DragStart = Input.Position
        StartPos = FovHolder.Position
        Input.Changed:Connect(function()
            if Input.UserInputState == Enum.UserInputState.End then Dragging = false end
        end)
    end)
    FovHolder.InputChanged:Connect(function(Input)
        if Input.UserInputType == Enum.UserInputType.MouseMovement
        or Input.UserInputType == Enum.UserInputType.Touch then
            DragInput = Input
        end
    end)
    UserInputService.InputChanged:Connect(function(Input)
        if Input == DragInput and Dragging then
            if DConfiguration.Combat.FovLocked then Dragging = false return end
            local Delta = Input.Position - DragStart
            FovHolder.Position = UDim2.new(
                StartPos.X.Scale, StartPos.X.Offset + Delta.X,
                StartPos.Y.Scale, StartPos.Y.Offset + Delta.Y
            )
        end
    end)
end

local function StopFovCircle()
    if FovHolder then FovHolder.Visible = false end
end

local function StartAimbot()
    if AimbotConn then AimbotConn:Disconnect(); AimbotConn = nil end
    AimbotConn = RunService.RenderStepped:Connect(function()
        if not DConfiguration.Combat.Aimbot then
            AimbotConn:Disconnect(); AimbotConn = nil; return
        end
        local KeyOk = not DConfiguration.Combat.RequireKeyHold or
            UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton2)
        if not KeyOk then return end
        local Target = GetClosestTarget()
        if not Target then return end
        local Part = GetAimPartFromTarget(Target)
        if not Part then return end
        local TargetCF = CFrame.new(Camera.CFrame.Position, Part.Position)
        Camera.CFrame = Camera.CFrame:Lerp(TargetCF, math.clamp(DConfiguration.Combat.Smoothness, 0.01, 1.0))
    end)
end

local function StopAimbot()
    if AimbotConn then AimbotConn:Disconnect(); AimbotConn = nil end
end

local function SetupSilentAim()
    if DConfiguration.Combat.SilentAimConnection then
        DConfiguration.Combat.SilentAimConnection:Disconnect()
        DConfiguration.Combat.SilentAimConnection = nil
    end
    if DConfiguration.Combat.SilentAim then
        DConfiguration.Combat.SilentAimConnection = RunService.Heartbeat:Connect(function()
            for _, player in pairs(Players:GetPlayers()) do
                if player ~= LocalPlayer and player.Character then
                    for _, part in pairs(player.Character:GetDescendants()) do
                        if part:IsA("BasePart") and (part.Name:match("UpperLeg") or part.Name == "HeadHB" or part.Name == "HumanoidRootPart") then
                            part.CanCollide = false
                            part.Transparency = 10
                            part.Size = Vector3.new(13, 13, 13)
                        end
                    end
                end
            end
        end)
    end
end

local function SetupInfiniteJump()
    if DConfiguration.Player.InfiniteJumpConnection then
        DConfiguration.Player.InfiniteJumpConnection:Disconnect()
        DConfiguration.Player.InfiniteJumpConnection = nil
    end
    if DConfiguration.Player.InfiniteJump then
        DConfiguration.Player.InfiniteJumpConnection = UserInputService.JumpRequest:Connect(function()
            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
                LocalPlayer.Character:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping")
            end
        end)
    end
end

local function SetupSpeedHack()
    if DConfiguration.Player.SpeedHackConnection then
        DConfiguration.Player.SpeedHackConnection:Disconnect()
        DConfiguration.Player.SpeedHackConnection = nil
    end
    local humanoid = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
    if humanoid and DConfiguration.Player.SpeedHack then
        humanoid.WalkSpeed = DConfiguration.Player.WalkSpeed
        DConfiguration.Player.SpeedHackConnection = humanoid:GetPropertyChangedSignal("WalkSpeed"):Connect(function()
            if DConfiguration.Player.SpeedHack then
                humanoid.WalkSpeed = DConfiguration.Player.WalkSpeed
            end
        end)
    end
end

local function SetupWeaponMods()
    for _, conn in pairs(DConfiguration.Weapon.WeaponConnections) do
        if conn then conn:Disconnect() end
    end
    DConfiguration.Weapon.WeaponConnections = {}

    local function GetArmsRoot()
        local cam = workspace.CurrentCamera
        return cam and cam:FindFirstChild("Arms")
    end
    local function GetEquippedTool()
        local char = LocalPlayer.Character
        return char and char:FindFirstChildOfClass("Tool")
    end

    if DConfiguration.Weapon.InfAmmo then
        DConfiguration.Weapon.WeaponConnections.InfAmmo = RunService.Heartbeat:Connect(function()
            -- PlayerGui Variables (primary)
            local gui = LocalPlayer.PlayerGui:FindFirstChild("GUI")
            if gui then
                local vars = gui:FindFirstChild("Client") and gui.Client:FindFirstChild("Variables")
                if vars then
                    if vars:FindFirstChild("ammocount")  then vars.ammocount.Value  = 999 end
                    if vars:FindFirstChild("ammocount2") then vars.ammocount2.Value = 999 end
                end
            end
            -- Arms viewmodel (backup)
            local arms = GetArmsRoot()
            if arms then
                for _, v in pairs(arms:GetDescendants()) do
                    if (v:IsA("IntValue") or v:IsA("NumberValue")) and v.Name:lower():find("ammo") then
                        v.Value = 999
                    end
                end
            end
        end)
    end

    if DConfiguration.Weapon.FireRate then
        DConfiguration.Weapon.WeaponConnections.FireRate = RunService.Heartbeat:Connect(function()
            -- Arms viewmodel (most reliable in Arsenal)
            local arms = GetArmsRoot()
            if arms then
                for _, v in pairs(arms:GetDescendants()) do
                    if v.Name == "FireRate" and (v:IsA("NumberValue") or v:IsA("IntValue")) then
                        v.Value = 0.001
                    end
                    if v.Name == "Auto" and v:IsA("BoolValue") then v.Value = true end
                    if v.Name == "Cooldown" and (v:IsA("NumberValue") or v:IsA("IntValue")) then
                        v.Value = 0
                    end
                end
            end
            -- Equipped tool
            local tool = GetEquippedTool()
            if tool then
                for _, v in pairs(tool:GetDescendants()) do
                    if v.Name == "FireRate" and (v:IsA("NumberValue") or v:IsA("IntValue")) then
                        v.Value = 0.001
                    end
                    if v.Name == "Auto" and v:IsA("BoolValue") then v.Value = true end
                end
            end
            -- ReplicatedStorage fallback
            local weapons = ReplicatedStorage:FindFirstChild("Weapons")
            if weapons then
                for _, v in pairs(weapons:GetDescendants()) do
                    if v.Name == "Auto" and v:IsA("BoolValue") then v.Value = true end
                    if v.Name == "FireRate" and (v:IsA("NumberValue") or v:IsA("IntValue")) then v.Value = 0.02 end
                end
            end
        end)
    end

    if DConfiguration.Weapon.NoRecoil then
        DConfiguration.Weapon.WeaponConnections.NoRecoil = RunService.Heartbeat:Connect(function()
            -- Arms viewmodel
            local arms = GetArmsRoot()
            if arms then
                for _, v in pairs(arms:GetDescendants()) do
                    if v.Name == "RecoilControl" and (v:IsA("NumberValue") or v:IsA("IntValue")) then v.Value = 0 end
                    if v.Name == "MaxSpread"     and (v:IsA("NumberValue") or v:IsA("IntValue")) then v.Value = 0 end
                    if v.Name == "MinSpread"     and (v:IsA("NumberValue") or v:IsA("IntValue")) then v.Value = 0 end
                    if v.Name == "Recoil"        and (v:IsA("NumberValue") or v:IsA("IntValue")) then v.Value = 0 end
                end
            end
            -- Equipped tool
            local tool = GetEquippedTool()
            if tool then
                for _, v in pairs(tool:GetDescendants()) do
                    if v.Name == "RecoilControl" and (v:IsA("NumberValue") or v:IsA("IntValue")) then v.Value = 0 end
                    if v.Name == "MaxSpread"     and (v:IsA("NumberValue") or v:IsA("IntValue")) then v.Value = 0 end
                    if v.Name == "MinSpread"     and (v:IsA("NumberValue") or v:IsA("IntValue")) then v.Value = 0 end
                end
            end
            -- ReplicatedStorage fallback
            local weapons = ReplicatedStorage:FindFirstChild("Weapons")
            if weapons then
                for _, v in pairs(weapons:GetDescendants()) do
                    if v.Name == "RecoilControl" and (v:IsA("NumberValue") or v:IsA("IntValue")) then v.Value = 0 end
                    if v.Name == "MaxSpread"     and (v:IsA("NumberValue") or v:IsA("IntValue")) then v.Value = 0 end
                end
            end
        end)
    end
end

local function SetupCharacterAdded()
    if DConfiguration.Player.CharacterAddedConnection then
        DConfiguration.Player.CharacterAddedConnection:Disconnect()
        DConfiguration.Player.CharacterAddedConnection = nil
    end
    DConfiguration.Player.CharacterAddedConnection = LocalPlayer.CharacterAdded:Connect(function(character)
        task.wait(0.5)
        SetupSpeedHack()
    end)
end

local function CalculateBoxScale(distance)
    if distance <= 20 then return 1 else return math.max(20 / distance, 0.25) end
end

local function Create2DBox(character, color, scale)
    local existing = character:FindFirstChild("Esp_2DBox")
    if existing then existing:Destroy() end
    local billboard = Instance.new("BillboardGui")
    billboard.Name = "Esp_2DBox"
    billboard.AlwaysOnTop = true
    billboard.Size = UDim2.new(0, 60 * scale, 0, 80 * scale)
    billboard.StudsOffset = Vector3.new(0, 0, 0)
    billboard.ClipsDescendants = false
    billboard.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    billboard.Active = true
    local rootPart = character:FindFirstChild("HumanoidRootPart")
    if rootPart then
        billboard.Adornee = rootPart
        billboard.Parent = rootPart
    else
        billboard.Adornee = character
        billboard.Parent = character
    end
    local frame = Instance.new("Frame")
    frame.Name = "BoxFrame"
    frame.Size = UDim2.new(1, 0, 1, 0)
    frame.BackgroundTransparency = 1
    frame.BorderSizePixel = 0
    frame.Parent = billboard
    local stroke = Instance.new("UIStroke")
    stroke.Thickness = math.max(1.5 * scale, 1)
    stroke.Transparency = 0.3
    stroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    stroke.Color = color
    stroke.Parent = frame
    return { billboard = billboard, frame = frame, stroke = stroke, scale = scale }
end

local function Update2DBox(boxData, color, scale)
    if boxData then
        if boxData.stroke then boxData.stroke.Color = color end
        if boxData.billboard then boxData.billboard.Size = UDim2.new(0, 60 * scale, 0, 80 * scale) end
        if boxData.stroke then boxData.stroke.Thickness = math.max(1.5 * scale, 1) end
        boxData.scale = scale
    end
end

local function Remove2DBox(character)
    local box = character:FindFirstChild("Esp_2DBox")
    if box then box:Destroy() end
    local rootPart = character:FindFirstChild("HumanoidRootPart")
    if rootPart then
        local boxInRoot = rootPart:FindFirstChild("Esp_2DBox")
        if boxInRoot then boxInRoot:Destroy() end
    end
end

local function Create3DBox(character, color, size)
    local folder = character:FindFirstChild("Esp_3DBox")
    if folder then folder:Destroy() end
    local rootPart = character:FindFirstChild("HumanoidRootPart")
    if not rootPart then return nil end
    folder = Instance.new("Folder")
    folder.Name = "Esp_3DBox"
    folder.Parent = character
    size = size or Vector3.new(3, 4, 2.5)
    local offsetX = size.X / 2
    local offsetY = size.Y / 2
    local offsetZ = size.Z / 2
    local edges = {
        {Vector3.new(0, offsetY, offsetZ), Vector3.new(size.X, 0.1, 0.1)},
        {Vector3.new(0, offsetY, -offsetZ), Vector3.new(size.X, 0.1, 0.1)},
        {Vector3.new(-offsetX, offsetY, 0), Vector3.new(0.1, 0.1, size.Z)},
        {Vector3.new(offsetX, offsetY, 0), Vector3.new(0.1, 0.1, size.Z)},
        {Vector3.new(0, -offsetY, offsetZ), Vector3.new(size.X, 0.1, 0.1)},
        {Vector3.new(0, -offsetY, -offsetZ), Vector3.new(size.X, 0.1, 0.1)},
        {Vector3.new(-offsetX, -offsetY, 0), Vector3.new(0.1, 0.1, size.Z)},
        {Vector3.new(offsetX, -offsetY, 0), Vector3.new(0.1, 0.1, size.Z)},
        {Vector3.new(-offsetX, 0, offsetZ), Vector3.new(0.1, size.Y, 0.1)},
        {Vector3.new(offsetX, 0, offsetZ), Vector3.new(0.1, size.Y, 0.1)},
        {Vector3.new(-offsetX, 0, -offsetZ), Vector3.new(0.1, size.Y, 0.1)},
        {Vector3.new(offsetX, 0, -offsetZ), Vector3.new(0.1, size.Y, 0.1)}
    }
    for _, edge in ipairs(edges) do
        local adornment = Instance.new("BoxHandleAdornment")
        adornment.Adornee = rootPart
        adornment.Size = edge[2]
        adornment.CFrame = CFrame.new(edge[1])
        adornment.Color3 = color
        adornment.Transparency = 0.2
        adornment.ZIndex = 10
        adornment.AlwaysOnTop = true
        adornment.Visible = true
        adornment.Parent = folder
    end
    return folder
end

local function Update3DBox(character, color)
    local folder = character:FindFirstChild("Esp_3DBox")
    if folder then
        for _, adornment in ipairs(folder:GetChildren()) do
            if adornment:IsA("BoxHandleAdornment") then
                adornment.Color3 = color
            end
        end
    end
end

local function Remove3DBox(character)
    local folder = character:FindFirstChild("Esp_3DBox")
    if folder then folder:Destroy() end
end

local function CreateBillboard(character, name, color)
    local existing = character:FindFirstChild("Esp_Billboard")
    if existing then existing:Destroy() end
    local billboard = Instance.new("BillboardGui")
    billboard.Name = "Esp_Billboard"
    billboard.AlwaysOnTop = true
    billboard.Size = UDim2.new(0, 200, 0, 40)
    billboard.StudsOffset = Vector3.new(0, 2.5, 0)
    billboard.ClipsDescendants = false
    billboard.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    billboard.Active = true
    local rootPart = character:FindFirstChild("HumanoidRootPart")
    if rootPart then
        billboard.Adornee = rootPart
        billboard.Parent = rootPart
    else
        billboard.Adornee = character
        billboard.Parent = character
    end
    local nameLabel = Instance.new("TextLabel")
    nameLabel.Name = "NameLabel"
    nameLabel.Size = UDim2.new(1, 0, 0, 20)
    nameLabel.Position = UDim2.new(0, 0, 0, 0)
    nameLabel.BackgroundTransparency = 1
    nameLabel.Text = name
    nameLabel.TextColor3 = color
    nameLabel.TextSize = 13
    nameLabel.Font = Enum.Font.GothamSemibold
    nameLabel.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
    nameLabel.TextStrokeTransparency = 0.3
    nameLabel.TextXAlignment = Enum.TextXAlignment.Center
    nameLabel.TextYAlignment = Enum.TextYAlignment.Bottom
    nameLabel.Parent = billboard
    local distanceLabel = Instance.new("TextLabel")
    distanceLabel.Name = "DistanceLabel"
    distanceLabel.Size = UDim2.new(1, 0, 0, 16)
    distanceLabel.Position = UDim2.new(0, 0, 0, 18)
    distanceLabel.BackgroundTransparency = 1
    distanceLabel.Text = ""
    distanceLabel.TextColor3 = color
    distanceLabel.TextSize = 11
    distanceLabel.Font = Enum.Font.Gotham
    distanceLabel.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
    distanceLabel.TextStrokeTransparency = 0.3
    distanceLabel.TextXAlignment = Enum.TextXAlignment.Center
    distanceLabel.TextYAlignment = Enum.TextYAlignment.Top
    distanceLabel.Parent = billboard
    return { billboard = billboard, nameLabel = nameLabel, distanceLabel = distanceLabel }
end

local function UpdateBillboard(billboardData, name, distance, color)
    if not billboardData then return end
    if name then
        billboardData.nameLabel.Text = name
        billboardData.nameLabel.TextColor3 = color
    end
    if distance then
        billboardData.distanceLabel.Text = string.format("%.0f studs", distance)
        billboardData.distanceLabel.TextColor3 = color
    end
    billboardData.nameLabel.Visible = name ~= nil
    billboardData.distanceLabel.Visible = distance ~= nil
end

local function RemoveBillboard(character)
    local bill = character:FindFirstChild("Esp_Billboard")
    if bill then bill:Destroy() end
    local rootPart = character:FindFirstChild("HumanoidRootPart")
    if rootPart then
        local billInRoot = rootPart:FindFirstChild("Esp_Billboard")
        if billInRoot then billInRoot:Destroy() end
    end
end

local function CreateHighlight(character, color)
    local existing = character:FindFirstChild("Esp_Highlight")
    if existing then existing:Destroy() end
    local highlight = Instance.new("Highlight")
    highlight.Name = "Esp_Highlight"
    highlight.Adornee = character
    highlight.FillColor = color
    highlight.OutlineColor = color
    highlight.FillTransparency = 0.5
    highlight.OutlineTransparency = 0.3
    highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    highlight.Parent = character
    return highlight
end

local function UpdateHighlight(highlight, color)
    if highlight then
        highlight.FillColor = color
        highlight.OutlineColor = color
    end
end

local function RemoveHighlight(character)
    local highlight = character:FindFirstChild("Esp_Highlight")
    if highlight then highlight:Destroy() end
end

local function CreateTracer2D(color, thickness)
    local line = Drawing.new("Line")
    line.Thickness = thickness or 1
    line.Color = color or Color3.fromRGB(255, 255, 255)
    line.Transparency = 1
    line.Visible = false
    return line
end

local function UpdateTracer2D(tracer, character, color)
    if not tracer then return end
    local hrp = character:FindFirstChild("HumanoidRootPart")
    if not hrp or not hrp:IsDescendantOf(Workspace) then
        tracer.Visible = false
        return
    end
    local screenPos, onScreen = Camera:WorldToViewportPoint(hrp.Position)
    if onScreen then
        local pos = DConfiguration.Esp.TracerPosition
        local vp = Camera.ViewportSize
        local from
        if pos == "Top" then from = Vector2.new(vp.X / 2, 0)
        elseif pos == "Center" then from = Vector2.new(vp.X / 2, vp.Y / 2)
        else from = Vector2.new(vp.X / 2, vp.Y) end
        if color then tracer.Color = color end
        tracer.Thickness = DConfiguration.Esp.TracerThickness
        tracer.From = from
        tracer.To = Vector2.new(screenPos.X, screenPos.Y)
        tracer.Visible = true
    else
        tracer.Visible = false
    end
end

local function RemoveTracer2D(tracer)
    if tracer then pcall(function() tracer:Remove() end) end
end

local function CleanupEsp()
    for character, esp in pairs(DConfiguration.Esp.Elements) do
        if esp.box2D then Remove2DBox(character) end
        if esp.box3D then Remove3DBox(character) end
        if esp.highlight then RemoveHighlight(character) end
        if esp.billboard then RemoveBillboard(character) end
        if esp.tracer2D then RemoveTracer2D(esp.tracer2D) end
    end
    DConfiguration.Esp.Elements = {}
end

local function GetDistanceFromCamera(position)
    local camera = workspace.CurrentCamera
    if not camera then return 0 end
    return (position - camera.CFrame.Position).Magnitude
end

local function GetTeamColor(team)
    if team and team.TeamColor then
        return team.TeamColor.Color
    end
    return Color3.fromRGB(255, 255, 255)
end

local function UpdateEsp()
    if not DConfiguration.Esp.Enabled then
        CleanupEsp()
        return
    end
    local camera = workspace.CurrentCamera
    if not camera then return end
    local currentTargets = {}
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local character = player.Character
            if character and character:FindFirstChild("HumanoidRootPart") then
                local humanoid = character:FindFirstChildOfClass("Humanoid")
                if humanoid and humanoid.Health > 0 then
                    if DConfiguration.Esp.TeamCheck and player.Team == LocalPlayer.Team then continue end
                    currentTargets[character] = true
                    if not DConfiguration.Esp.Elements[character] then
                        DConfiguration.Esp.Elements[character] = {}
                    end
                    local esp = DConfiguration.Esp.Elements[character]
                    local distance = GetDistanceFromCamera(character.HumanoidRootPart.Position)
                    local scale = CalculateBoxScale(distance)
                    local color = GetTeamColor(player.Team)
                    if DConfiguration.Esp.BoxesEnabled then
                        if DConfiguration.Esp.BoxType == "2D" then
                            if not esp.box2D then esp.box2D = Create2DBox(character, color, scale) end
                            if esp.box2D then Update2DBox(esp.box2D, color, scale) end
                            if esp.box3D then Remove3DBox(character); esp.box3D = nil end
                        else
                            local boxSize = Vector3.new(3, 4, 2.5)
                            if humanoid then boxSize = Vector3.new(2.5, humanoid.HipHeight + 4, 2) end
                            if not esp.box3D then esp.box3D = Create3DBox(character, color, boxSize) end
                            if esp.box3D then Update3DBox(character, color) end
                            if esp.box2D then Remove2DBox(character); esp.box2D = nil end
                        end
                    else
                        if esp.box2D then Remove2DBox(character); esp.box2D = nil end
                        if esp.box3D then Remove3DBox(character); esp.box3D = nil end
                    end
                    if DConfiguration.Esp.HighlightsEnabled then
                        if not esp.highlight then esp.highlight = CreateHighlight(character, color) end
                        if esp.highlight then UpdateHighlight(esp.highlight, color) end
                    else
                        if esp.highlight then RemoveHighlight(character); esp.highlight = nil end
                    end
                    if DConfiguration.Esp.TracerEnabled then
                        if not esp.tracer2D then
                            esp.tracer2D = CreateTracer2D(color, DConfiguration.Esp.TracerThickness)
                        end
                        if esp.tracer2D then UpdateTracer2D(esp.tracer2D, character, color) end
                    else
                        if esp.tracer2D then RemoveTracer2D(esp.tracer2D); esp.tracer2D = nil end
                    end

                    if DConfiguration.Esp.NamesEnabled or DConfiguration.Esp.DistanceEnabled then
                        if not esp.billboard then esp.billboard = CreateBillboard(character, player.Name, color) end
                        if esp.billboard then
                            local displayDistance = DConfiguration.Esp.DistanceEnabled and distance or nil
                            UpdateBillboard(esp.billboard, DConfiguration.Esp.NamesEnabled and player.Name or nil, displayDistance, color)
                        end
                    else
                        if esp.billboard then RemoveBillboard(character); esp.billboard = nil end
                    end
                end
            end
        end
    end
    local charsToRemove = {}
    for character, esp in pairs(DConfiguration.Esp.Elements) do
        if not currentTargets[character] then
            if esp.box2D then Remove2DBox(character) end
            if esp.box3D then Remove3DBox(character) end
            if esp.highlight then RemoveHighlight(character) end
            if esp.billboard then RemoveBillboard(character) end
            if esp.tracer2D then RemoveTracer2D(esp.tracer2D) end
            table.insert(charsToRemove, character)
        end
    end
    for _, character in ipairs(charsToRemove) do
        DConfiguration.Esp.Elements[character] = nil
    end
end

local function StartEspRender()
    if DConfiguration.Esp.RenderConnection then return end
    DConfiguration.Esp.RenderConnection = RunService.RenderStepped:Connect(UpdateEsp)
end

local function StopEspRender()
    if DConfiguration.Esp.RenderConnection then
        DConfiguration.Esp.RenderConnection:Disconnect()
        DConfiguration.Esp.RenderConnection = nil
    end
    CleanupEsp()
end

local function CaptureAndForce(part, color, store)
    if not store[part] then
        store[part] = { Kind = "Part", Material = part.Material, Color = part.Color }
    end
    part.Material = Enum.Material.ForceField
    part.Color = color
    for _, child in pairs(part:GetChildren()) do
        if child:IsA("Decal") or child:IsA("Texture") then
            if not store[child] then store[child] = { Kind = "Texture", Transparency = child.Transparency } end
            child.Transparency = 1
        elseif child:IsA("SurfaceAppearance") then
            if not store[child] then store[child] = { Kind = "Surface", Parent = child.Parent } end
            child.Parent = nil
        end
    end
end

local function RestoreAll(store)
    for obj, data in pairs(store) do
        if obj then
            if data.Kind == "Part" and obj.Parent then
                obj.Material = data.Material
                obj.Color = data.Color
            elseif data.Kind == "Texture" and obj.Parent then
                obj.Transparency = data.Transparency
            elseif data.Kind == "Surface" then
                obj.Parent = data.Parent
            end
        end
    end
end

local function GetPlayerNames()
    local names = { "All" }
    for _, plr in ipairs(Players:GetPlayers()) do
        table.insert(names, plr.Name)
    end
    return names
end

local function IsSelected(plr)
    if DConfiguration.Forcefield.Selected["All"] then return true end
    return DConfiguration.Forcefield.Selected[plr.Name] == true
end

local function IsNpcCharacter(character)
    if not character then return false end
    return Players:GetPlayerFromCharacter(character) == nil
end

local function GetAllBaseParts(root)
    local parts = {}
    if root:IsA("BasePart") then table.insert(parts, root) end
    for _, part in pairs(root:GetDescendants()) do
        if part:IsA("BasePart") then table.insert(parts, part) end
    end
    return parts
end

local ArmTargetNames = { "CSSArms", "HumanoidRootPart", "Left Arm", "Right Arm" }
local ArmTargetNameSet = {}
for _, name in ipairs(ArmTargetNames) do ArmTargetNameSet[name] = true end

local function GetArmTargets(armsRoot)
    local targets = {}
    for _, inst in pairs(armsRoot:GetDescendants()) do
        if ArmTargetNameSet[inst.Name] then table.insert(targets, inst) end
    end
    return targets
end

local function IsUnderAnyTarget(part, targets)
    for _, target in pairs(targets) do
        if part == target or part:IsDescendantOf(target) then return true end
    end
    return false
end

local function GetPlayerColor()
    if DConfiguration.Forcefield.UseRGB then
        return Color3.fromHSV((tick() * DConfiguration.Forcefield.RGBSpeed) % 1, 1, 1)
    end
    return DConfiguration.Forcefield.Color
end

local function GetGunColor()
    if DConfiguration.Gun.UseRGB then
        return Color3.fromHSV((tick() * DConfiguration.Gun.RGBSpeed) % 1, 1, 1)
    end
    return DConfiguration.Gun.Color
end

local function StopEffect(character)
    if not character then return end
    if DConfiguration.Forcefield.Connections[character] then
        DConfiguration.Forcefield.Connections[character]:Disconnect()
        DConfiguration.Forcefield.Connections[character] = nil
    end
    if DConfiguration.Forcefield.DiedConn[character] then
        DConfiguration.Forcefield.DiedConn[character]:Disconnect()
        DConfiguration.Forcefield.DiedConn[character] = nil
    end
    local originals = DConfiguration.Forcefield.Originals[character]
    if originals then
        RestoreAll(originals)
        DConfiguration.Forcefield.Originals[character] = nil
    end
end

local function StartEffect(character)
    if not character or DConfiguration.Forcefield.Connections[character] then return end
    local humanoid = character:FindFirstChild("Humanoid")
    if not humanoid then return end
    DConfiguration.Forcefield.Originals[character] = {}
    DConfiguration.Forcefield.Connections[character] = RunService.Heartbeat:Connect(function()
        local color = GetPlayerColor()
        local store = DConfiguration.Forcefield.Originals[character]
        for _, part in pairs(character:GetDescendants()) do
            if part:IsA("BasePart") then CaptureAndForce(part, color, store) end
        end
    end)
    DConfiguration.Forcefield.DiedConn[character] = humanoid.Died:Connect(function()
        StopEffect(character)
    end)
end

local function RefreshPlayers()
    for _, plr in ipairs(Players:GetPlayers()) do
        if DConfiguration.Forcefield.ApplyEnabled and IsSelected(plr) then
            StartEffect(plr.Character)
        else
            StopEffect(plr.Character)
        end
    end
end

local function GetNpcCharacters()
    local npcs = {}
    for _, humanoid in pairs(workspace:GetDescendants()) do
        if humanoid:IsA("Humanoid") then
            local character = humanoid.Parent
            if IsNpcCharacter(character) then npcs[character] = true end
        end
    end
    return npcs
end

local function RefreshNpcs()
    if DConfiguration.Forcefield.ApplyEnabled and DConfiguration.Forcefield.IncludeNpc then
        for character in pairs(GetNpcCharacters()) do
            StartEffect(character)
            DConfiguration.Forcefield.NpcCharacters[character] = true
        end
    else
        for character in pairs(DConfiguration.Forcefield.NpcCharacters) do
            StopEffect(character)
        end
        DConfiguration.Forcefield.NpcCharacters = {}
    end
end

local function StopArmsEffect()
    if DConfiguration.Arms.Connection then
        DConfiguration.Arms.Connection:Disconnect()
        DConfiguration.Arms.Connection = nil
    end
    RestoreAll(DConfiguration.Arms.Originals)
    DConfiguration.Arms.Originals = {}
end

local function StartArmsEffect()
    if DConfiguration.Arms.Connection then return end
    DConfiguration.Arms.Connection = RunService.Heartbeat:Connect(function()
        local camera = workspace.CurrentCamera
        local armsRoot = camera and camera:FindFirstChild("Arms")
        if not armsRoot then return end
        local color = GetPlayerColor()
        local armTargets = GetArmTargets(armsRoot)
        for _, target in pairs(armTargets) do
            for _, part in pairs(GetAllBaseParts(target)) do
                CaptureAndForce(part, color, DConfiguration.Arms.Originals)
            end
        end
    end)
end

local function RefreshArms()
    if DConfiguration.Forcefield.ApplyEnabled and IsSelected(LocalPlayer) then
        StartArmsEffect()
    else
        StopArmsEffect()
    end
end

local function GetGunParts(armsRoot)
    local armTargets = GetArmTargets(armsRoot)
    local parts = {}
    for _, part in pairs(armsRoot:GetDescendants()) do
        if part:IsA("BasePart") and not IsUnderAnyTarget(part, armTargets) then
            table.insert(parts, part)
        end
    end
    return parts
end

local function StopGunEffect()
    if DConfiguration.Gun.Connection then
        DConfiguration.Gun.Connection:Disconnect()
        DConfiguration.Gun.Connection = nil
    end
    RestoreAll(DConfiguration.Gun.Originals)
    DConfiguration.Gun.Originals = {}
end

local function StartGunEffect()
    if DConfiguration.Gun.Connection then return end
    DConfiguration.Gun.Connection = RunService.Heartbeat:Connect(function()
        local camera = workspace.CurrentCamera
        local armsRoot = camera and camera:FindFirstChild("Arms")
        if not armsRoot then return end
        local color = GetGunColor()
        for _, part in pairs(GetGunParts(armsRoot)) do
            CaptureAndForce(part, color, DConfiguration.Gun.Originals)
        end
    end)
end

local function RefreshGun()
    if DConfiguration.Gun.Enabled then StartGunEffect() else StopGunEffect() end
end

local function StopRGBGun()
    if DConfiguration.RGBGun.Connection then
        DConfiguration.RGBGun.Connection:Disconnect()
        DConfiguration.RGBGun.Connection = nil
    end
    for part, origColor in pairs(DConfiguration.RGBGun.Originals) do
        if part and part.Parent then part.Color = origColor end
    end
    DConfiguration.RGBGun.Originals = {}
end

local function StartRGBGun()
    if DConfiguration.RGBGun.Connection then return end
    DConfiguration.RGBGun.Connection = RunService.Heartbeat:Connect(function()
        local cam = workspace.CurrentCamera
        local armsRoot = cam and cam:FindFirstChild("Arms")
        if not armsRoot then return end
        local hue = (tick() * DConfiguration.RGBGun.Speed) % 1
        local color = Color3.fromHSV(hue, 1, 1)
        for _, part in pairs(GetGunParts(armsRoot)) do
            if part:IsA("BasePart") then
                if not DConfiguration.RGBGun.Originals[part] then
                    DConfiguration.RGBGun.Originals[part] = part.Color
                end
                part.Color = color
            end
        end
    end)
end

local function RefreshRGBGun()
    if DConfiguration.RGBGun.Enabled then StartRGBGun() else StopRGBGun() end
end

local function HookPlayer(plr)
    if DConfiguration.Forcefield.CharAddedConn[plr] then return end
    DConfiguration.Forcefield.CharAddedConn[plr] = plr.CharacterAdded:Connect(function(character)
        task.wait(0.15)
        if DConfiguration.Forcefield.ApplyEnabled and IsSelected(plr) then StartEffect(character) end
    end)
    if plr.Character and DConfiguration.Forcefield.ApplyEnabled and IsSelected(plr) then
        StartEffect(plr.Character)
    end
end

local function UnhookPlayer(plr)
    StopEffect(plr.Character)
    if DConfiguration.Forcefield.CharAddedConn[plr] then
        DConfiguration.Forcefield.CharAddedConn[plr]:Disconnect()
        DConfiguration.Forcefield.CharAddedConn[plr] = nil
    end
    DConfiguration.Forcefield.Selected[plr.Name] = nil
end

local function RefreshDropdownValues(ffDropdown)
    local newValues = GetPlayerNames()
    pcall(function() ffDropdown:Refresh(newValues, false) end)
    pcall(function() ffDropdown:SetValues(newValues) end)
end

local secForcefield = Tabs.Visual:AddSection("Forcefield Player Effect", "solar/shield-star-bold")

local ffDropdown = secForcefield:AddDropdown("FF_PlayerSelect", {
    ThemedDropdown = true,
    Search = true,
    Title = "Select Players",
    Icon = "solar/users-group-rounded-bold",
    Multi = true,
    DropdownOutsideWindow = true,
    Values = GetPlayerNames(),
    Default = {},
    Description = "Select which players to apply the effect to.",
    Callback = function(v)
        DConfiguration.Forcefield.Selected = {}
        for name, isChecked in next, v do
            if isChecked then DConfiguration.Forcefield.Selected[name] = true end
        end
        RefreshPlayers()
        RefreshArms()
    end,
})

secForcefield:AddToggle("FF_Apply", {
    Title = "Apply Forcefield Effect",
    Icon = "solar/check-square-bold",
    Default = false,
    Description = "Apply a Forcefield effect to selected players.",
    Callback = function(v)
        DConfiguration.Forcefield.ApplyEnabled = v
        RefreshPlayers()
        RefreshNpcs()
        RefreshArms()
    end,
})

secForcefield:AddToggle("FF_IncludeNpc", {
    Title = "Include Npc",
    Icon = "solar/user-bold",
    Default = false,
    Description = "Also apply the effect to NPCs.",
    Callback = function(v)
        DConfiguration.Forcefield.IncludeNpc = v
        RefreshNpcs()
    end,
})

secForcefield:AddColorpicker("FF_Color", {
    Title = "Forcefield Color",
    Icon = "solar/palette-bold",
    Default = DConfiguration.Forcefield.Color,
    Transparency = 0,
    Description = "Base color when RGB is disabled.",
    Callback = function(c)
        DConfiguration.Forcefield.Color = c
    end,
})

secForcefield:AddToggle("FF_RGBRainbow", {
    Title = "RGB Rainbow Color",
    Icon = "solar/palette-2-bold",
    Default = false,
    Description = "Cycle through rainbow colors automatically.",
    Callback = function(v)
        DConfiguration.Forcefield.UseRGB = v
    end,
})

secForcefield:AddInput("FF_RGBSpeed", {
    Title = "RGB Speed",
    Icon = "solar/speedometer-bold",
    Placeholder = "1",
    Default = tostring(DConfiguration.Forcefield.RGBSpeed),
    Description = "How fast the RGB color cycles.",
    Callback = function(v)
        local speed = tonumber(v)
        if speed then DConfiguration.Forcefield.RGBSpeed = speed end
    end,
})

secForcefield:AddDivider()

for _, plr in ipairs(Players:GetPlayers()) do HookPlayer(plr) end

Players.PlayerAdded:Connect(function(plr)
    HookPlayer(plr)
    RefreshDropdownValues(ffDropdown)
end)

Players.PlayerRemoving:Connect(function(plr)
    UnhookPlayer(plr)
    RefreshDropdownValues(ffDropdown)
end)

workspace.DescendantAdded:Connect(function(descendant)
    if descendant:IsA("Humanoid") then
        task.wait(0.15)
        local character = descendant.Parent
        if DConfiguration.Forcefield.ApplyEnabled and DConfiguration.Forcefield.IncludeNpc and IsNpcCharacter(character) then
            StartEffect(character)
            DConfiguration.Forcefield.NpcCharacters[character] = true
        end
    end
end)

local secForcefieldGun = Tabs.Visual:AddSection("Forcefield Effect (Gun)", "solar/gun-bold")

secForcefieldGun:AddToggle("FF_GunApply", {
    Title = "Apply Gun Forcefield",
    Icon = "solar/check-square-bold",
    Default = false,
    Description = "Apply Forcefield to your gun viewmodel.",
    Callback = function(v)
        DConfiguration.Gun.Enabled = v
        RefreshGun()
    end,
})

secForcefieldGun:AddColorpicker("FF_GunColor", {
    Title = "Forcefield Color",
    Icon = "solar/palette-bold",
    Default = DConfiguration.Gun.Color,
    Transparency = 0,
    Description = "Base color when RGB is disabled.",
    Callback = function(c)
        DConfiguration.Gun.Color = c
    end,
})

secForcefieldGun:AddToggle("FF_GunRGBRainbow", {
    Title = "RGB Rainbow Color",
    Icon = "solar/palette-2-bold",
    Default = false,
    Description = "Cycle through rainbow colors on the gun.",
    Callback = function(v)
        DConfiguration.Gun.UseRGB = v
    end,
})

secForcefieldGun:AddInput("FF_GunRGBSpeed", {
    Title = "RGB Speed",
    Icon = "solar/speedometer-bold",
    Placeholder = "1",
    Default = tostring(DConfiguration.Gun.RGBSpeed),
    Description = "How fast the gun RGB color cycles.",
    Callback = function(v)
        local speed = tonumber(v)
        if speed then DConfiguration.Gun.RGBSpeed = speed end
    end,
})


local secRGBGun = Tabs.Visual:AddSection("RGB Gun", "lucide/zap")

secRGBGun:AddToggle("RGBGun_Enable", {
    Title = "Enable RGB Gun",
    Icon = "lucide/zap",
    Default = false,
    Callback = function(v)
        DConfiguration.RGBGun.Enabled = v
        RefreshRGBGun()
    end
})

secRGBGun:AddInput("RGBGun_Speed", {
    Title = "RGB Speed",
    Icon = "lucide/gauge",
    Default = "1",
    Numeric = true,
    Finished = false,
    Callback = function(v)
        local n = tonumber(v)
        if n then DConfiguration.RGBGun.Speed = math.max(0.1, n) end
    end
})

local function StopEnemyEffect(character)
    if not character then return end
    if DConfiguration.ForcefieldEnemy.Connections[character] then
        DConfiguration.ForcefieldEnemy.Connections[character]:Disconnect()
        DConfiguration.ForcefieldEnemy.Connections[character] = nil
    end
    if DConfiguration.ForcefieldEnemy.DiedConn[character] then
        DConfiguration.ForcefieldEnemy.DiedConn[character]:Disconnect()
        DConfiguration.ForcefieldEnemy.DiedConn[character] = nil
    end
    local originals = DConfiguration.ForcefieldEnemy.Originals[character]
    if originals then
        RestoreAll(originals)
        DConfiguration.ForcefieldEnemy.Originals[character] = nil
    end
end

local function StartEnemyEffect(character)
    if not character or DConfiguration.ForcefieldEnemy.Connections[character] then return end
    local humanoid = character:FindFirstChild("Humanoid")
    if not humanoid then return end
    local player = Players:GetPlayerFromCharacter(character)
    if not player or player == LocalPlayer then return end
    local teamColor = GetTeamColor(player.Team)
    DConfiguration.ForcefieldEnemy.Originals[character] = {}
    DConfiguration.ForcefieldEnemy.Connections[character] = RunService.Heartbeat:Connect(function()
        local store = DConfiguration.ForcefieldEnemy.Originals[character]
        for _, part in pairs(character:GetDescendants()) do
            if part:IsA("BasePart") then CaptureAndForce(part, teamColor, store) end
        end
    end)
    DConfiguration.ForcefieldEnemy.DiedConn[character] = humanoid.Died:Connect(function()
        StopEnemyEffect(character)
    end)
end

local function RefreshEnemyEffect()
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer and plr.Character then
            if DConfiguration.ForcefieldEnemy.ApplyEnabled then
                StartEnemyEffect(plr.Character)
            else
                StopEnemyEffect(plr.Character)
            end
        end
    end
end

local function HookEnemyPlayer(plr)
    if DConfiguration.ForcefieldEnemy.CharAddedConn[plr] then return end
    DConfiguration.ForcefieldEnemy.CharAddedConn[plr] = plr.CharacterAdded:Connect(function(character)
        task.wait(0.15)
        if DConfiguration.ForcefieldEnemy.ApplyEnabled and plr ~= LocalPlayer then StartEnemyEffect(character) end
    end)
    if plr.Character and DConfiguration.ForcefieldEnemy.ApplyEnabled and plr ~= LocalPlayer then
        StartEnemyEffect(plr.Character)
    end
end

local function UnhookEnemyPlayer(plr)
    StopEnemyEffect(plr.Character)
    if DConfiguration.ForcefieldEnemy.CharAddedConn[plr] then
        DConfiguration.ForcefieldEnemy.CharAddedConn[plr]:Disconnect()
        DConfiguration.ForcefieldEnemy.CharAddedConn[plr] = nil
    end
end

secForcefield:AddToggle("FF_EnemyApply", {
    Title = "Apply Enemy Forcefield",
    Icon = "solar/check-square-bold",
    Default = false,
    Description = "Apply a team-colored Forcefield to all enemies.",
    Callback = function(v)
        DConfiguration.ForcefieldEnemy.ApplyEnabled = v
        RefreshEnemyEffect()
    end,
})

for _, plr in ipairs(Players:GetPlayers()) do HookEnemyPlayer(plr) end

Players.PlayerAdded:Connect(function(plr)
    HookEnemyPlayer(plr)
end)

Players.PlayerRemoving:Connect(function(plr)
    UnhookEnemyPlayer(plr)
end)

local secCombat = Tabs.Combat:AddSection("Combat Settings", "lucide/sword")

secCombat:AddToggle("Combat_Aimbot", {
    Title = "Enable Aimbot",
    Icon = "lucide/crosshair",
    Default = false,
    Callback = function(v)
        DConfiguration.Combat.Aimbot = v
        if v then StartAimbot() else StopAimbot() end
    end
})

secCombat:AddToggle("Combat_SilentAim", {
    Title = "OP Silent Aim",
    Icon = "lucide/eye-off",
    Default = false,
    Callback = function(v)
        DConfiguration.Combat.SilentAim = v
        SetupSilentAim()
    end
})

secCombat:AddToggle("Combat_TeamCheck", {
    Title = "Team Check",
    Icon = "lucide/users",
    Default = false,
    Callback = function(v)
        DConfiguration.Combat.TeamCheck = v
    end
})

local BlacklistDropdown = secCombat:AddDropdown("Combat_BlacklistTeams", {
    ThemedDropdown = true,
    Search = true,
    DropdownOutsideWindow = true,
    Title = "Team Blacklist",
    Icon = "lucide/shield-off",
    Multi = true,
    Values = (function()
        local Names = {}
        for _, Team in ipairs(game:GetService("Teams"):GetTeams()) do
            table.insert(Names, Team.Name)
        end
        return Names
    end)(),
    Default = {},
    Callback = function(v)
        DConfiguration.Combat.BlacklistedTeams = {}
        for TeamName, Selected in pairs(v) do
            if Selected then
                DConfiguration.Combat.BlacklistedTeams[TeamName] = true
            end
        end
    end
})

game:GetService("Teams").ChildAdded:Connect(function(Team)
    if BlacklistDropdown and BlacklistDropdown.AddValue then
        pcall(function() BlacklistDropdown:AddValue(Team.Name) end)
    end
end)
game:GetService("Teams").ChildRemoved:Connect(function(Team)
    DConfiguration.Combat.BlacklistedTeams[Team.Name] = nil
    if BlacklistDropdown and BlacklistDropdown.RemoveValue then
        pcall(function() BlacklistDropdown:RemoveValue(Team.Name) end)
    end
end)

secCombat:AddDropdown("Combat_AimPart", {
    ThemedDropdown = true,
    Search = false,
    Title = "Aim Target",
    Icon = "lucide/body",
    Values = { "Head", "HumanoidRootPart", "UpperTorso", "LowerTorso" },
    Default = "Head",
    Callback = function(v)
        DConfiguration.Combat.AimPart = v
    end
})

secCombat:AddInput("Combat_Sensitivity", {
    Title = "Aimbot Sensitivity",
    Icon = "lucide/gauge",
    Default = "1",
    Numeric = true,
    Finished = false,
    Callback = function(v)
        local n = tonumber(v)
        if n then
            DConfiguration.Combat.Smoothness = math.clamp(n, 0.01, 1.0)
        end
    end
})

secCombat:AddSlider("Combat_MaxDistance", {
    Title = "Max Distance",
    Icon = "lucide/scale",
    Min = 100,
    Max = 5000,
    Default = 1000,
    Rounding = 10,
    Callback = function(v)
        DConfiguration.Combat.MaxDistance = tonumber(v) or 1000
    end
})

secCombat:AddToggle("Combat_WallCheck", {
    Title = "Wall Check",
    Icon = "lucide/wall",
    Default = false,
    Callback = function(v)
        DConfiguration.Combat.WallCheck = v
    end
})

secCombat:AddToggle("Combat_RequireKeyHold", {
    Title = "Require Key Hold",
    Icon = "lucide/key",
    Default = false,
    Description = "Hold right-click to activate the aimbot.",
    Callback = function(v)
        DConfiguration.Combat.RequireKeyHold = v
    end
})

secCombat:AddDivider()

local secFov = Tabs.Combat:AddSection("FOV Circle", "lucide/circle")

secFov:AddToggle("Fov_Show", {
    Title = "Show FOV Circle",
    Icon = "lucide/circle",
    Default = false,
    Callback = function(v)
        DConfiguration.Combat.ShowFOV = v
        if v then StartFovCircle() else StopFovCircle() end
    end
})

secFov:AddInput("Fov_Size", {
    Title = "FOV Size",
    Icon = "lucide/radius",
    Default = "150",
    Numeric = true,
    Finished = false,
    Callback = function(v)
        local n = tonumber(v)
        if n and n > 0 then
            DConfiguration.Combat.FovSize = n
            if FovHolder then
                FovHolder.Size = UDim2.fromOffset(n * 2, n * 2)
            end
        end
    end
})

secFov:AddColorpicker("Fov_Color", {
    Title = "FOV Color",
    Icon = "lucide/palette",
    Default = Color3.fromRGB(255, 220, 0),
    Callback = function(v)
        DConfiguration.Combat.FovColor = v
        if not DConfiguration.Combat.FovRGB then
            if FovRing then FovRing.BackgroundColor3 = v end
            if FovStroke then FovStroke.Color = v end
        end
    end
})

secFov:AddToggle("Fov_RGB", {
    Title = "RGB Color",
    Icon = "lucide/zap",
    Default = false,
    Callback = function(v)
        DConfiguration.Combat.FovRGB = v
        if not v then
            if FovRing then FovRing.BackgroundColor3 = DConfiguration.Combat.FovColor end
            if FovStroke then FovStroke.Color = DConfiguration.Combat.FovColor end
        end
    end
})

secFov:AddToggle("Fov_Lock", {
    Title = "Lock in Center",
    Icon = "lucide/lock",
    Default = false,
    Callback = function(v)
        DConfiguration.Combat.FovLocked = v
        if v and FovHolder then
            FovHolder.Position = UDim2.fromScale(0.5, 0.5)
        end
    end
})

local secEsp = Tabs.Combat:AddSection("ESP Settings", "lucide/eye")

secEsp:AddToggle("Esp_Enable", {
    Title = "Enable ESP",
    Icon = "lucide/eye",
    Default = false,
    Callback = function(v)
        DConfiguration.Esp.Enabled = v
        if v then StartEspRender() else StopEspRender() end
    end
})

secEsp:AddToggle("Esp_TeamCheck", {
    Title = "Team Check",
    Icon = "lucide/users",
    Default = false,
    Callback = function(v)
        DConfiguration.Esp.TeamCheck = v
    end
})


secEsp:AddDropdown("Esp_BoxType", {
    ThemedDropdown = true,
    Title = "Box Type",
    Search = false,
    Icon = "lucide/layers",
    Values = { "2D", "3D" },
    Default = "2D",
    Callback = function(v)
        DConfiguration.Esp.BoxType = v
    end
})

secEsp:AddToggle("Esp_Boxes", {
    Title = "Show Boxes",
    Icon = "lucide/square",
    Default = false,
    Callback = function(v)
        DConfiguration.Esp.BoxesEnabled = v
    end
})

secEsp:AddToggle("Esp_Names", {
    Title = "Show Names",
    Icon = "lucide/text",
    Default = false,
    Callback = function(v)
        DConfiguration.Esp.NamesEnabled = v
    end
})

secEsp:AddToggle("Esp_Distance", {
    Title = "Show Distance",
    Icon = "lucide/ruler",
    Default = false,
    Callback = function(v)
        DConfiguration.Esp.DistanceEnabled = v
    end
})

secEsp:AddToggle("Esp_Highlights", {
    Title = "Show Highlights",
    Icon = "lucide/star",
    Default = false,
    Callback = function(v)
        DConfiguration.Esp.HighlightsEnabled = v
    end
})

secEsp:AddToggle("Esp_Tracer", {
    Title = "Tracer",
    Icon = "lucide/move-diagonal",
    Default = false,
    Callback = function(v)
        DConfiguration.Esp.TracerEnabled = v
    end
})

secEsp:AddDropdown("Esp_TracerPos", {
    ThemedDropdown = true,
    Search = false,
    Title = "Tracer Origin",
    Icon = "lucide/align-center-vertical",
    Values = { "Bottom", "Center", "Top" },
    Default = "Bottom",
    Callback = function(v)
        DConfiguration.Esp.TracerPosition = v
    end
})


secEsp:AddInput("Esp_TracerThick", {
    Title = "Tracer Thickness",
    Icon = "lucide/minus",
    Default = "1",
    Numeric = true,
    Finished = false,
    Callback = function(v)
        local n = tonumber(v)
        if n then DConfiguration.Esp.TracerThickness = math.max(0.5, n) end
    end
})

secEsp:AddDivider()

local secWeaponMods = Tabs.Main:AddSection("Gun Modifications", "lucide/gun")

secWeaponMods:AddToggle("Weapon_InfAmmo", {
    Title = "Infinite Ammo",
    Icon = "lucide/ammo",
    Tag = "Not Working",
    Default = false,
    Callback = function(v)
        DConfiguration.Weapon.InfAmmo = v
        SetupWeaponMods()
    end
})

secWeaponMods:AddToggle("Weapon_FireRate", {
    Title = "FireRate Mod",
    Icon = "lucide/clock",
    Default = false,
    Callback = function(v)
        DConfiguration.Weapon.FireRate = v
        SetupWeaponMods()
    end
})

secWeaponMods:AddToggle("Weapon_NoRecoil", {
    Title = "No Recoil",
    Icon = "lucide/aim",
    Default = false,
    Callback = function(v)
        DConfiguration.Weapon.NoRecoil = v
        SetupWeaponMods()
    end
})

secWeaponMods:AddDivider()

local secPlayerMods = Tabs.Main:AddSection("Player", "lucide/user")

secPlayerMods:AddToggle("Player_SpeedHack", {
    Title = "Speed Hack",
    Icon = "lucide/running",
    Default = false,
    Callback = function(v)
        DConfiguration.Player.SpeedHack = v
        SetupSpeedHack()
        if not v then
            local humanoid = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
            if humanoid then humanoid.WalkSpeed = 16 end
        end
        if SpeedHackFB_Btn then FB.SetActive(SpeedHackFB_Btn, v, "Speed Hack") end
    end
})

secPlayerMods:AddSlider("Player_WalkSpeed", {
    Title = "Speed Value",
    Icon = "lucide/gauge",
    Min = 16,
    Max = 200,
    Default = 16,
    Rounding = 1,
    Callback = function(v)
        DConfiguration.Player.WalkSpeed = tonumber(v) or 16
        if DConfiguration.Player.SpeedHack then
            local humanoid = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
            if humanoid then humanoid.WalkSpeed = tonumber(v) or 16 end
        end
    end
})

secPlayerMods:AddDivider()

secPlayerMods:AddToggle("FB_SpeedHack_Show", {
    Title = "Speed Hack Button",
    Icon = "lucide/running",
    Default = false,
    Callback = function(v)
        if SpeedHackFB_Frame then FB.SetVisible(SpeedHackFB_Frame, v) end
    end
})

secPlayerMods:AddInput("SpeedHack_Size", {
    Title = "Button Size",
    Icon = "lucide/layout",
    Placeholder = "200x70",
    Default = "200x70",
    Finished = true,
    Callback = function(Value)
        local W, H = tostring(Value):match("^(%d+)[xX](%d+)$")
        W, H = tonumber(W), tonumber(H)
        if W and H and W > 0 and H > 0 then
            if FB and FB.FloatButtonSizes and FB.FloatButtonSizes["SpeedHack"] then
                local Entry = FB.FloatButtonSizes["SpeedHack"]
                Entry.W = W
                Entry.H = H
                if Entry.Frame and not Entry.Frame:GetAttribute("IsCircle") then
                    Entry.Frame.Size = UDim2.new(0, W, 0, H)
                end
            end
        end
    end,
})

secPlayerMods:AddKeybind("SpeedHack_Keybind", {
    Title = "Speed Hack Keybind",
    Icon = "lucide/keyboard",
    Default = "",
    Callback = function()
        if not SpeedHackFB_Btn then return end
        DConfiguration.Player.SpeedHack = not DConfiguration.Player.SpeedHack
        FB.SetActive(SpeedHackFB_Btn, DConfiguration.Player.SpeedHack, "Speed Hack")
        SetupSpeedHack()
        if not DConfiguration.Player.SpeedHack then
            local humanoid = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
            if humanoid then humanoid.WalkSpeed = 16 end
        end
    end,
})

secPlayerMods:AddToggle("Player_InfiniteJump", {
    Title = "Infinite Jump",
    Icon = "lucide/arrow-up",
    Default = false,
    Callback = function(v)
        DConfiguration.Player.InfiniteJump = v
        SetupInfiniteJump()
        if InfJumpFB_Btn then FB.SetActive(InfJumpFB_Btn, v, "Infinite Jump") end
    end
})

secPlayerMods:AddDivider()

secPlayerMods:AddToggle("FB_InfJump_Show", {
    Title = "Infinite Jump Button",
    Icon = "lucide/arrow-up",
    Default = false,
    Callback = function(v)
        if InfJumpFB_Frame then FB.SetVisible(InfJumpFB_Frame, v) end
    end
})

secPlayerMods:AddInput("InfiniteJump_Size", {
    Title = "Button Size",
    Icon = "lucide/layout",
    Placeholder = "200x70",
    Default = "200x70",
    Finished = true,
    Callback = function(Value)
        local W, H = tostring(Value):match("^(%d+)[xX](%d+)$")
        W, H = tonumber(W), tonumber(H)
        if W and H and W > 0 and H > 0 then
            if FB and FB.FloatButtonSizes and FB.FloatButtonSizes["InfiniteJump"] then
                local Entry = FB.FloatButtonSizes["InfiniteJump"]
                Entry.W = W
                Entry.H = H
                if Entry.Frame and not Entry.Frame:GetAttribute("IsCircle") then
                    Entry.Frame.Size = UDim2.new(0, W, 0, H)
                end
            end
        end
    end,
})

secPlayerMods:AddKeybind("InfiniteJump_Keybind", {
    Title = "Infinite Jump Keybind",
    Icon = "lucide/keyboard",
    Default = "",
    Callback = function()
        if not InfJumpFB_Btn then return end
        DConfiguration.Player.InfiniteJump = not DConfiguration.Player.InfiniteJump
        FB.SetActive(InfJumpFB_Btn, DConfiguration.Player.InfiniteJump, "Infinite Jump")
        SetupInfiniteJump()
    end,
})

local function InitializeCombat()
    SetupWeaponMods()
    SetupCharacterAdded()
    SetupSilentAim()
    SetupInfiniteJump()
    SetupSpeedHack()
end

InitializeCombat()

FpsData = {
    GUI = nil,
    Connection = nil,
}

local FpsStartTime = tick()

function ToggleFPSCounter(State)
    if not State then
        if FpsData.GUI then
            FpsData.GUI:Destroy()
            FpsData.GUI = nil
        end
        if FpsData.Connection then
            FpsData.Connection:Disconnect()
            FpsData.Connection = nil
        end
        return
    end

    if State and not FpsData.GUI then
        local FpsCounter = Instance.new("ScreenGui")
        FpsCounter.Name = "FPSCounter"
        FpsCounter.Parent = game.CoreGui
        FpsCounter.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
        FpsCounter.ResetOnSpawn = false
        FpsData.GUI = FpsCounter

        local Frame = Instance.new("Frame")
        Frame.Parent = FpsCounter
        Frame.Size = UDim2.new(0, 180, 0, 80)
        Frame.Position = UDim2.new(0, 300, 0, 10)
        Frame.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
        Frame.BackgroundTransparency = 0.7

        local Corner = Instance.new("UICorner", Frame)
        Corner.CornerRadius = UDim.new(0, 15)

        local Gradient = Instance.new("UIGradient", Frame)
        Gradient.Color = (Fluent:GetButtonGradient() or Fluent.ButtonGradients).Background

        local UIStroke = Instance.new("UIStroke", Frame)
        UIStroke.Thickness = 2
        UIStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border

        local GradientStroke = Instance.new("UIGradient", UIStroke)
        GradientStroke.Color = (Fluent:GetButtonGradient() or Fluent.ButtonGradients).Stroke

        task.spawn(function()
            while FpsCounter and FpsCounter.Parent do
                Gradient.Rotation = (Gradient.Rotation + 1) % 360
                Gradient.Color = (Fluent:GetButtonGradient() or Fluent.ButtonGradients).Background
                task.wait(0.03)
            end
        end)

        task.spawn(function()
            while FpsCounter and FpsCounter.Parent do
                GradientStroke.Rotation = (GradientStroke.Rotation + 0.5) % 360
                GradientStroke.Color = (Fluent:GetButtonGradient() or Fluent.ButtonGradients).Stroke
                task.wait()
            end
        end)

        local Label = Instance.new("TextLabel", Frame)
        Label.Size = UDim2.new(1, -10, 1, -10)
        Label.Position = UDim2.new(0, 5, 0, 5)
        Label.BackgroundTransparency = 1
        Label.TextColor3 = Color3.fromRGB(255, 255, 255)
        Label.Font = Enum.Font.GothamBlack
        Label.TextSize = 12
        Label.TextXAlignment = Enum.TextXAlignment.Center
        Label.TextYAlignment = Enum.TextYAlignment.Center
        Label.Text = "Loading..."

        if typeof(MakeDraggable) == "function" then
            MakeDraggable(Frame, Frame, false)
        end

        local LastUpdateTime = tick()
        local FrameCount = 0

        FpsData.Connection = RunService.RenderStepped:Connect(function()
            FrameCount = FrameCount + 1
            local Now = tick()
            local Dt = Now - LastUpdateTime

            if Dt >= 1 then
                local Fps = math.round(FrameCount / Dt)
                local Elapsed = Now - FpsStartTime
                local H = math.floor(Elapsed / 3600)
                local M = math.floor((Elapsed % 3600) / 60)
                local S = math.floor(Elapsed % 60)

                local Ping = 0
                pcall(function()
                    Ping = math.floor(Stats.Network.ServerStatsItem["Data Ping"]:GetValue())
                end)

                Label.Text = string.format("FPS: %d | Ping: %d ms\nClient Timer: %dh %dm %ds", Fps, Ping, H, M, S)
                LastUpdateTime = Now
                FrameCount = 0
            end
        end)
    end
end

ToggleFPSCounter(true)

local FloatingButtonModule = loadstring(game:HttpGet("https://raw.githubusercontent.com/StyearX/GoonWares/refs/heads/main/Module/FloatingButton.lua"))()
FB = FloatingButtonModule.new(Fluent, FloatingButtonManager)

SpeedHackFB_Frame, SpeedHackFB_Btn = FB:Create("SpeedHack", "Speed Hack", true, function(Btn)
    DConfiguration.Player.SpeedHack = not DConfiguration.Player.SpeedHack
    FB.SetActive(Btn, DConfiguration.Player.SpeedHack, "Speed Hack")
    SetupSpeedHack()
    if not DConfiguration.Player.SpeedHack then
        local humanoid = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
        if humanoid then humanoid.WalkSpeed = 16 end
    end
end)
FB.SetVisible(SpeedHackFB_Frame, false)

InfJumpFB_Frame, InfJumpFB_Btn = FB:Create("InfiniteJump", "Infinite Jump", true, function(Btn)
    DConfiguration.Player.InfiniteJump = not DConfiguration.Player.InfiniteJump
    FB.SetActive(Btn, DConfiguration.Player.InfiniteJump, "Infinite Jump")
    SetupInfiniteJump()
end)
FB.SetVisible(InfJumpFB_Frame, false)

SecPerformance = Tabs.Settings:AddSection("Performance", "solar/cpu-bold")
SecPerformance:AddToggle("FPSCounterToggle", {
    Title = "FPS Counter",
    Default = false,
    Callback = function(Value)
        ToggleFPSCounter(Value)
    end,
})

SecDiscord = Tabs.Info:AddSection("Discord", "solar/chat-round-bold")
SecDiscord:AddDiscord({ InviteCode = "QBhcVu6c" })
SecDiscord:AddDivider()
SecDiscord:AddSpace({ Height = 20 })

SecTikTok = Tabs.Info:AddSection("TikTok", "solar/music-note-bold")
SecTikTok:AddImage({ Image = "https://unavatar.io/tiktok/styearx", AspectRatio = "16:9", Radius = 10 })
SecTikTok:AddParagraph({ Title = "Follow my main account Pls", Content = "@styearx" })
SecTikTok:AddCode({
    Title = "",
    Code = "https://www.tiktok.com/@styearx",
    OnCopy = function() Notify("Link", "Copied", "Info", nil, 2) end,
})
SecTikTok:AddDivider()
SecTikTok:AddSpace({ Height = 20 })

SecYouTube = Tabs.Info:AddSection("YouTube", "solar/videocamera-record-bold")
SecYouTube:AddImage({ Image = "https://unavatar.io/youtube/StyearX", AspectRatio = "16:9", Radius = 10 })
SecYouTube:AddParagraph({ Title = "Subscribe to my main account :)", Content = "@StyearX" })
SecYouTube:AddCode({
    Title = "",
    Code = "https://m.youtube.com/@styearx",
    OnCopy = function() Notify("Link", "Copied", "Info", nil, 2) end,
})
SecYouTube:AddDivider()
SecYouTube:AddSpace({ Height = 20 })

SecGithub = Tabs.Info:AddSection("GitHub", "solar/code-square-bold")
SecGithub:AddImage({ Image = "https://unavatar.io/github/StyearX", AspectRatio = "16:9", Radius = 10 })
SecGithub:AddParagraph({ Title = "Check out my GitHub", Content = "@StyearX" })
SecGithub:AddCode({
    Title = "",
    Code = "https://github.com/StyearX",
    OnCopy = function() Notify("Link", "Copied", "Info", nil, 2) end,
})
SecGithub:AddDivider()
SecGithub:AddSpace({ Height = 20 })

SecUILibrary = Tabs.Info:AddSection("UI Library", "solar/widget-bold")
SecUILibrary:AddParagraph({ Title = "Fluent Modded", Content = "The UI Library used in this script." })
SecUILibrary:AddCode({
    Title = "",
    Code = "https://github.com/StyearX/Fluent-modded",
    OnCopy = function() Notify("Link", "Copied", "Info", nil, 2) end,
})

BuiltInSkyboxes = {
    ["Waguri"] = {
        Folder = "GoonWares/Skyboxes/Waguri",
        Faces = {
            { Prop = "SkyboxBk", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/Waguri/waguri_ft.png", File = "waguri_ft.png" },
            { Prop = "SkyboxFt", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/Waguri/waguri_bk.png", File = "waguri_bk.png" },
            { Prop = "SkyboxLf", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/Waguri/waguri_rt.png", File = "waguri_rt.png" },
            { Prop = "SkyboxRt", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/Waguri/waguri_lf.png", File = "waguri_lf.png" },
            { Prop = "SkyboxUp", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/Waguri/waguri_up.png", File = "waguri_up.png" },
            { Prop = "SkyboxDn", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/Waguri/waguri_dn.png", File = "waguri_dn.png" },
        },
    },
    ["ItsukiNakano"] = {
        Folder = "GoonWares/Skyboxes/ItsukiNakano",
        Faces = {
            { Prop = "SkyboxBk", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/ItsukiNakano/ItsukiNakano_Bk.png", File = "ItsukiNakano_Bk.png" },
            { Prop = "SkyboxFt", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/ItsukiNakano/ItsukiNakano_Ft.png", File = "ItsukiNakano_Ft.png" },
            { Prop = "SkyboxLf", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/ItsukiNakano/ItsukiNakano_Lf.png", File = "ItsukiNakano_Lf.png" },
            { Prop = "SkyboxRt", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/ItsukiNakano/ItsukiNakano_Rt.png", File = "ItsukiNakano_Rt.png" },
            { Prop = "SkyboxUp", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/ItsukiNakano/ItsukiNakano_Up.png", File = "ItsukiNakano_Up.png" },
            { Prop = "SkyboxDn", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/ItsukiNakano/ItsukiNakano_Dn.png", File = "ItsukiNakano_Dn.png" },
        },
    },
    ["ItsukiNakano2"] = {
        Folder = "GoonWares/Skyboxes/ItsukiNakano2",
        Faces = {
            { Prop = "SkyboxBk", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/ItsukiNakano2/ItsukiNakano2_Bk.png", File = "ItsukiNakano2_Bk.png" },
            { Prop = "SkyboxFt", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/ItsukiNakano2/ItsukiNakano2_Ft.png", File = "ItsukiNakano2_Ft.png" },
            { Prop = "SkyboxLf", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/ItsukiNakano2/ItsukiNakano2_Lf.png", File = "ItsukiNakano2_Lf.png" },
            { Prop = "SkyboxRt", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/ItsukiNakano2/ItsukiNakano2_Rt.png", File = "ItsukiNakano2_Rt.png" },
            { Prop = "SkyboxUp", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/ItsukiNakano2/ItsukiNakano2_Up.png", File = "ItsukiNakano2_Up.png" },
            { Prop = "SkyboxDn", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/ItsukiNakano2/ItsukiNakano2_Dn.png", File = "ItsukiNakano2_Dn.png" },
        },
    },
    ["MaiSakurajima"] = {
        Folder = "GoonWares/Skyboxes/MaiSakurajima",
        Faces = {
            { Prop = "SkyboxUp", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/MaiSakurajima/top.png", File = "top.png" },
            { Prop = "SkyboxDn", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/MaiSakurajima/down.png", File = "down.png" },
            { Prop = "SkyboxLf", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/MaiSakurajima/left.png", File = "left.png" },
            { Prop = "SkyboxRt", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/MaiSakurajima/right.png", File = "right.png" },
            { Prop = "SkyboxFt", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/MaiSakurajima/front.png", File = "front.png" },
            { Prop = "SkyboxBk", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/MaiSakurajima/back.png", File = "back.png" },
        },
    },
    ["MikuNakano"] = {
        Folder = "GoonWares/Skyboxes/MikuNakano",
        Faces = {
            { Prop = "SkyboxBk", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/MikuNakano/MikuNakano_Bk.png", File = "MikuNakano_Bk.png" },
            { Prop = "SkyboxFt", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/MikuNakano/MikuNakano_Ft.png", File = "MikuNakano_Ft.png" },
            { Prop = "SkyboxLf", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/MikuNakano/MikuNakano_Lf.png", File = "MikuNakano_Lf.png" },
            { Prop = "SkyboxRt", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/MikuNakano/MikuNakano_Rt.png", File = "MikuNakano_Rt.png" },
            { Prop = "SkyboxUp", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/MikuNakano/MikuNakano_Up.png", File = "MikuNakano_Up.png" },
            { Prop = "SkyboxDn", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/MikuNakano/MikuNakano_Dn.png", File = "MikuNakano_Dn.png" },
        },
    },
    ["TohkaYatogami"] = {
        Folder = "GoonWares/Skyboxes/TohkaYatogami",
        Faces = {
            { Prop = "SkyboxBk", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/TohkaYatogami/TohkaYatogami_BK.png", File = "TohkaYatogami_BK.png" },
            { Prop = "SkyboxFt", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/TohkaYatogami/TohkaYatogami_FT.png", File = "TohkaYatogami_FT.png" },
            { Prop = "SkyboxLf", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/TohkaYatogami/TohkaYatogami_LF.png", File = "TohkaYatogami_LF.png" },
            { Prop = "SkyboxRt", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/TohkaYatogami/TohkaYatogami_RT.png", File = "TohkaYatogami_RT.png" },
            { Prop = "SkyboxUp", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/TohkaYatogami/TohkaYatogami_UP.png", File = "TohkaYatogami_UP.png" },
            { Prop = "SkyboxDn", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/TohkaYatogami/TohkaYatogami_DN.png", File = "TohkaYatogami_DN.png" },
        },
    },
    ["TohkaYatogami2"] = {
        Folder = "GoonWares/Skyboxes/TohkaYatogami",
        Faces = {
            { Prop = "SkyboxBk", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/TohkaYatogami/TohkaYatogami2_BK.png", File = "TohkaYatogami2_BK.png" },
            { Prop = "SkyboxFt", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/TohkaYatogami/TohkaYatogami2_FT.png", File = "TohkaYatogami2_FT.png" },
            { Prop = "SkyboxLf", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/TohkaYatogami/TohkaYatogami2_LF.png", File = "TohkaYatogami2_LF.png" },
            { Prop = "SkyboxRt", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/TohkaYatogami/TohkaYatogami2_RT.png", File = "TohkaYatogami2_RT.png" },
            { Prop = "SkyboxUp", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/TohkaYatogami/TohkaYatogami2_UP.png", File = "TohkaYatogami2_UP.png" },
            { Prop = "SkyboxDn", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/TohkaYatogami/TohkaYatogami2_DN.png", File = "TohkaYatogami2_DN.png" },
        },
    },
    ["LilithAsami"] = {
        Folder = "GoonWares/Skyboxes/LilithAsami",
        Faces = {
            { Prop = "SkyboxBk", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/LilithAsami/LilithAsami_BK.png", File = "LilithAsami_BK.png" },
            { Prop = "SkyboxFt", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/LilithAsami/LilithAsami_FT.png", File = "LilithAsami_FT.png" },
            { Prop = "SkyboxLf", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/LilithAsami/LilithAsami_LF.png", File = "LilithAsami_LF.png" },
            { Prop = "SkyboxRt", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/LilithAsami/LilithAsami_RT.png", File = "LilithAsami_RT.png" },
            { Prop = "SkyboxUp", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/LilithAsami/LilithAsami_UP.png", File = "LilithAsami_UP.png" },
            { Prop = "SkyboxDn", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/LilithAsami/LilithAsami_DN.png", File = "LilithAsami_DN.png" },
        },
    },
    ["Evernight"] = {
        Folder = "GoonWares/Skyboxes/Evernight",
        Faces = {
            { Prop = "SkyboxBk", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/Evernight/evernight_RT.png", File = "SkyRt.png" },
            { Prop = "SkyboxRt", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/Evernight/evernight_LF.png", File = "SkyIf.png" },
            { Prop = "SkyboxLf", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/Evernight/evernight_BK.png", File = "SkyBk.png" },
            { Prop = "SkyboxFt", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/Evernight/evernight_FT.png", File = "SkyFt.png" },
            { Prop = "SkyboxUp", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/Evernight/evernight_UP.png", File = "SkyUp.png" },
            { Prop = "SkyboxDn", Url = "https://raw.githubusercontent.com/StyearX/Custom-skybox/main/Evernight/evernight_DN.png", File = "SkyDn.png" },
        },
    },
    ["Xenovia Quarta"] = {
        Folder = "GoonWares/Skyboxes/Xenovia Quarta",
        Faces = {
            { Prop = "SkyboxLf", Url = "https://od.lk/d/NjNfOTg0NjM0ODhf/if.png", File = "if.png" },
            { Prop = "SkyboxBk", Url = "https://od.lk/d/NjNfOTg0NjM0ODlf/ft.png", File = "ft.png" },
            { Prop = "SkyboxDn", Url = "https://od.lk/d/NjNfOTg0NjM0OTBf/dn.png", File = "dn.png" },
            { Prop = "SkyboxFt", Url = "https://od.lk/d/NjNfOTg0NjM0OTFf/bk.png", File = "bk.png" },
            { Prop = "SkyboxUp", Url = "https://od.lk/d/NjNfOTg0NjM0ODZf/up.png", File = "up.png" },
            { Prop = "SkyboxRt", Url = "https://od.lk/d/NjNfOTg0NjM0ODdf/rt.png", File = "rt.png" },
        },
    },
    ["Nino Nakano"] = {
        Folder = "GoonWares/Skyboxes/Nino Nakano",
        ResetHaze = true,
        Faces = {
            { Prop = "SkyboxRt", Url = "https://od.lk/d/NjNfOTg0NjQyODNf/right1.png", File = "right1.png" },
            { Prop = "SkyboxLf", Url = "https://od.lk/d/NjNfOTg0NjQyNzhf/back.png", File = "back.png" },
            { Prop = "SkyboxFt", Url = "https://od.lk/d/NjNfOTg0NjQyODBf/front.png", File = "front.png" },
            { Prop = "SkyboxBk", Url = "https://od.lk/d/NjNfOTg0NjQyODJf/left1.png", File = "left1.png" },
            { Prop = "SkyboxDn", Url = "https://od.lk/d/NjNfOTg0NjQyNzlf/down.png", File = "down.png" },
            { Prop = "SkyboxUp", Url = "https://od.lk/d/NjNfOTg0NjQyODFf/up.png", File = "up.png" },
        },
    },
    ["Nino Nakano 2"] = {
        Folder = "GoonWares/Skyboxes/Nino Nakano 2",
        ResetHaze = true,
        Faces = {
            { Prop = "SkyboxRt", Url = "https://od.lk/d/NjNfOTg0NjQyNTJf/rt.png", File = "rt.png" },
            { Prop = "SkyboxUp", Url = "https://od.lk/d/NjNfOTg0NjQyNTBf/up.png", File = "up.png" },
            { Prop = "SkyboxDn", Url = "https://od.lk/d/NjNfOTg0NjQyNTVf/dn.png", File = "dn.png" },
            { Prop = "SkyboxBk", Url = "https://od.lk/d/NjNfOTg0NjQyNTNf/if.png", File = "if.png" },
            { Prop = "SkyboxFt", Url = "https://od.lk/d/NjNfOTg0NjQyNTZf/bk.png", File = "bk.png" },
            { Prop = "SkyboxLf", Url = "https://od.lk/d/NjNfOTg0NjQyNTRf/ft.png", File = "ft.png" },
        },
    },
    ["Saki Saki"] = {
        Folder = "GoonWares/Skyboxes/Saki Saki",
        ResetHaze = true,
        Faces = {
            { Prop = "SkyboxRt", Url = "https://od.lk/d/NjNfOTg0NjQyNzNf/right.png", File = "right.png" },
            { Prop = "SkyboxLf", Url = "https://od.lk/d/NjNfOTg0NjQyNzBf/left.png", File = "left.png" },
            { Prop = "SkyboxFt", Url = "https://od.lk/d/NjNfOTg0NjQyNjhf/front1.png", File = "front1.png" },
            { Prop = "SkyboxBk", Url = "https://od.lk/d/NjNfOTg0NjQyNjZf/back1.png", File = "back1.png" },
            { Prop = "SkyboxDn", Url = "https://od.lk/d/NjNfOTg0NjQyNjdf/down1.png", File = "down1.png" },
            { Prop = "SkyboxUp", Url = "https://od.lk/d/NjNfOTg0NjQyNjlf/Up1.png", File = "Up1.png" },
        },
    },
    ["Rias Gremory"] = {
        Folder = "GoonWares/Skyboxes/Rias Gremory",
        ResetHaze = true,
        Faces = {
            { Prop = "SkyboxRt", Url = "https://od.lk/d/NjNfOTg0NzkyOTFf/RightRias.png", File = "RightRias.png" },
            { Prop = "SkyboxBk", Url = "https://od.lk/d/NjNfOTg0NzkyOTNf/leftRias.png", File = "leftRias.png" },
            { Prop = "SkyboxFt", Url = "https://od.lk/d/NjNfOTg0NzkyOTVf/front%20Rias.png", File = "frontRias.png" },
            { Prop = "SkyboxLf", Url = "https://od.lk/d/NjNfOTg0NzkyOTRf/BackRias.png", File = "BackRias.png" },
            { Prop = "SkyboxDn", Url = "https://od.lk/d/NjNfOTg0NzkyOThf/downRias.png", File = "downRias.png" },
            { Prop = "SkyboxUp", Url = "https://od.lk/d/NjNfOTg0NzkyOTJf/UpRias.png", File = "UpRias.png" },
        },
    },
    ["Yotsuba Nakano"] = {
        Folder = "GoonWares/Skyboxes/Yotsuba Nakano",
        ResetHaze = true,
        Faces = {
            { Prop = "SkyboxRt", Url = "https://od.lk/d/NjNfOTg0NzkzMzdf/YotsubaRt.png", File = "YotsubaRt.png" },
            { Prop = "SkyboxUp", Url = "https://od.lk/d/NjNfOTg0NzkzMzhf/YotsubaUp.png", File = "YotsubaUp.png" },
            { Prop = "SkyboxDn", Url = "https://od.lk/d/NjNfOTg0NzkzMzNf/YotsubaDn.png", File = "YotsubaDn.png" },
            { Prop = "SkyboxFt", Url = "https://od.lk/d/NjNfOTg0NzkzMzRf/YotsubaFt.png", File = "YotsubaFt.png" },
            { Prop = "SkyboxBk", Url = "https://od.lk/d/NjNfOTg0NzkzMzFf/YotsubaBk.png", File = "YotsubaBk.png" },
            { Prop = "SkyboxLf", Url = "https://od.lk/d/NjNfOTg0NzkzMzZf/YotsubaLeft.png", File = "YotsubaLeft.png" },
        },
    },
    ["Hakari Hananozo"] = {
        Folder = "GoonWares/Skyboxes/hk",
        Faces = {
            { Prop = "SkyboxBk", Url = "https://od.lk/d/NjNfOTg0NjEzMTVf/SkyBk.tex", File = "SkyBk.png" },
            { Prop = "SkyboxFt", Url = "https://od.lk/s/NjNfOTg0NjEzMTdf/SkyFt.tex", File = "SkyFt.png" },
            { Prop = "SkyboxLf", Url = "https://od.lk/s/NjNfOTg0NjEzMThf/SkyIf.tex", File = "SkyIf.png" },
            { Prop = "SkyboxRt", Url = "https://od.lk/d/NjNfOTg0NjEzMTlf/SkyRt.tex", File = "SkyRt.png" },
            { Prop = "SkyboxUp", Url = "https://od.lk/d/NjNfOTg0NjEzMjBf/SkyUp.tex", File = "SkyUp.png" },
            { Prop = "SkyboxDn", Url = "https://od.lk/d/NjNfOTg0NjEzMTZf/Skydn.tex", File = "Skydn.png" },
        },
    },
    ["Alya"] = {
        Folder = "GoonWares/Skyboxes/Alya",
        ResetHaze = true,
        Faces = {
            { Prop = "SkyboxRt", Url = "https://od.lk/d/NjNfOTg0NzkzMTZf/Rt.png", File = "Rt.png" },
            { Prop = "SkyboxUp", Url = "https://od.lk/d/NjNfOTg0NzkzMTVf/Up.png", File = "Up.png" },
            { Prop = "SkyboxDn", Url = "https://od.lk/d/NjNfOTg0NzkzMTJf/dn.png", File = "dn.png" },
            { Prop = "SkyboxFt", Url = "https://od.lk/d/NjNfOTg0NzkzMTNf/ft.png", File = "ft.png" },
            { Prop = "SkyboxBk", Url = "https://od.lk/d/NjNfOTg0NzkzMTFf/bk.png", File = "bk.png" },
            { Prop = "SkyboxLf", Url = "https://od.lk/d/NjNfOTg0NzkzMTRf/if.png", File = "if.png" },
        },
    },
    ["Alya 2"] = {
        Folder = "GoonWares/Skyboxes/Alya 2",
        ResetHaze = true,
        Faces = {
            { Prop = "SkyboxRt", Url = "https://od.lk/d/NjNfOTg0NzkzMjRf/AlyaRt.png", File = "AlyaRt.png" },
            { Prop = "SkyboxUp", Url = "https://od.lk/d/NjNfOTg0NzkzMjVf/AlyaUp.png", File = "AlyaUp.png" },
            { Prop = "SkyboxDn", Url = "https://od.lk/d/NjNfOTg0NzkzMjFf/alyaDn.png", File = "alyaDn.png" },
            { Prop = "SkyboxFt", Url = "https://od.lk/d/NjNfOTg0NzkzMjJf/AlyaFt.png", File = "AlyaFt.png" },
            { Prop = "SkyboxBk", Url = "https://od.lk/d/NjNfOTg0NzkzMjBf/AlyaBk.png", File = "AlyaBk.png" },
            { Prop = "SkyboxLf", Url = "https://od.lk/d/NjNfOTg0NzkzMjNf/AlyaLf.png", File = "AlyaLf.png" },
        },
    },
    ["Iteration 3.2.0"] = {
        RbxAssetIds = {
            SkyboxLf = "rbxassetid://111969418190645",
            SkyboxFt = "rbxassetid://86300370158690",
            SkyboxRt = "rbxassetid://90660761614046",
            SkyboxBk = "rbxassetid://94155581950702",
            SkyboxDn = "rbxassetid://74377450776557",
            SkyboxUp = "rbxassetid://110123630908028",
        },
        StarCount = 3000,
        SunAngularSize = 0,
        MoonAngularSize = 0,
    },
    ["IterationT Remake"] = {
        RbxAssetIds = {
            SkyboxLf = "rbxassetid://131895067742893",
            SkyboxFt = "rbxassetid://139394863985793",
            SkyboxRt = "rbxassetid://93313124175539",
            SkyboxBk = "rbxassetid://93271230842735",
            SkyboxDn = "rbxassetid://72142305760362",
            SkyboxUp = "rbxassetid://86656757951125",
        },
        StarCount = 3000,
        SunAngularSize = 0,
        MoonAngularSize = 0,
    },
}

function ApplyBuiltInSkybox(data)
    for _, v in pairs(Lighting:GetChildren()) do
        if v:IsA("Sky") then v:Destroy() end
    end

    if data.ResetHaze then
        for _, v in pairs(Lighting:GetChildren()) do
            if v:IsA("Atmosphere") then v.Haze = 0 end
        end
    end

    local SkyInstance = Instance.new("Sky")
    SkyInstance.Name = "Sky"
    SkyInstance.CelestialBodiesShown = false
    SkyInstance.StarCount = data.StarCount or 0
    if data.SunAngularSize then SkyInstance.SunAngularSize = data.SunAngularSize end
    if data.MoonAngularSize then SkyInstance.MoonAngularSize = data.MoonAngularSize end

    if data.RbxAssetIds then
        for prop, id in pairs(data.RbxAssetIds) do
            SkyInstance[prop] = id
        end
    elseif data.Faces then
        pcall(function() makefolder("GoonWares/Skyboxes") end)
        pcall(function() makefolder(data.Folder) end)
        for _, face in ipairs(data.Faces) do
            local path = data.Folder .. "/" .. face.File
            if not isfile(path) then
                writefile(path, game:HttpGet(face.Url, true))
            end
            SkyInstance[face.Prop] = getcustomasset(path)
        end
    end

    SkyInstance.Parent = Lighting
end

SecSkyboxChanger = Tabs.Visual:AddSection("Skybox Changer", "solar/sun-bold")
SecSkyboxChanger:AddSpace({ Height = 20 })
SecSkyboxChanger:AddDivider()

BuiltInSkyboxNames = {}
for skyboxName in pairs(BuiltInSkyboxes) do
    table.insert(BuiltInSkyboxNames, skyboxName)
end
table.sort(BuiltInSkyboxNames)

SelectedBuiltInSkybox = BuiltInSkyboxNames[1]
SecSkyboxChanger:AddDropdown("BuiltInSkyboxDropdown", {
    ThemedDropdown = true,
    DropdownOutsideWindow = true,
    Search = true,
    Title = "Built-in Skybox",
    Values = BuiltInSkyboxNames,
    Default = SelectedBuiltInSkybox,
    Callback = function(value)
        SelectedBuiltInSkybox = value
    end
})

SecSkyboxChanger:AddButton({
    Title = "Apply Skybox",
    Callback = function()
        local data = BuiltInSkyboxes[SelectedBuiltInSkybox]
        if data then
            local ok = pcall(ApplyBuiltInSkybox, data)
            if ok then
                Fluent:Notify({ Title = "Skybox", Content = SelectedBuiltInSkybox .. " applied.", Duration = 3 })
            else
                Fluent:Notify({ Title = "Skybox", Content = "Failed to apply skybox.", Duration = 3 })
            end
        end
    end
})

SkyboxForceEnabled = false
SkyboxForceConnection = nil
SkyboxApplyingNow = false

function ApplySelectedSkyboxSafely()
    local data = BuiltInSkyboxes[SelectedBuiltInSkybox]
    if not data then return end
    SkyboxApplyingNow = true
    pcall(ApplyBuiltInSkybox, data)
    task.wait(0.2)
    SkyboxApplyingNow = false
end

SecSkyboxChanger:AddToggle("SkyboxForceToggle", {Title = "Force Skybox Every Round", Default = false}):OnChanged(function(State)
    SkyboxForceEnabled = State
    if SkyboxForceConnection then
        SkyboxForceConnection:Disconnect()
        SkyboxForceConnection = nil
    end
    if State then
        ApplySelectedSkyboxSafely()
        SkyboxForceConnection = Lighting.ChildAdded:Connect(function(child)
            if not SkyboxForceEnabled then return end
            if SkyboxApplyingNow then return end
            if child:IsA("Sky") then
                task.wait(0.1)
                ApplySelectedSkyboxSafely()
            end
        end)
    end
end)

SecSkyboxChanger:AddParagraph({
    Title = "Built-in Skybox Info",
    Content = "Apply custom skyboxes. Some may take a moment to load. Use Force mode to keep it after round changes."
})

SecSkyboxChanger:AddSpace({ Height = 20 })
SecSkyboxChanger:AddDivider()
SecSkyboxChanger:AddSpace({ Height = 20 })

SecCustomSkybox = Tabs.Visual:AddSection("Custom Skybox", "solar/gallery-bold")
SecCustomSkybox:AddSpace({ Height = 20 })
SecCustomSkybox:AddDivider()

CustomSkyboxInputs = { Lf = "", Rt = "", Up = "", Dn = "", Ft = "", Bk = "" }

function ResolveSkyboxInput(value, faceName)
    value = tostring(value or ""):gsub("^%s*(.-)%s*$", "%1")
    if value == "" then return nil end
    if value:match("^rbxassetid://") then
        return value
    elseif value:match("^%d+$") then
        return "rbxassetid://" .. value
    elseif value:match("^https?://") then
        local ok = pcall(function() makefolder("GoonWares/Skyboxes/Custom") end)
        local path = "GoonWares/Skyboxes/Custom/" .. faceName .. ".png"
        local downloadOk = pcall(function()
            writefile(path, game:HttpGet(value, true))
        end)
        if downloadOk then
            return getcustomasset(path)
        end
    end
    return nil
end

function ApplyCustomSkybox()
    local propMap = { Lf = "SkyboxLf", Rt = "SkyboxRt", Up = "SkyboxUp", Dn = "SkyboxDn", Ft = "SkyboxFt", Bk = "SkyboxBk" }

    for _, v in pairs(Lighting:GetChildren()) do
        if v:IsA("Sky") then v:Destroy() end
    end

    local SkyInstance = Instance.new("Sky")
    SkyInstance.Name = "Sky"
    SkyInstance.CelestialBodiesShown = false
    SkyInstance.StarCount = 0

    local appliedAny = false
    for face, prop in pairs(propMap) do
        local resolved = ResolveSkyboxInput(CustomSkyboxInputs[face], face)
        if resolved then
            SkyInstance[prop] = resolved
            appliedAny = true
        end
    end

    if appliedAny then
        SkyInstance.Parent = Lighting
        Fluent:Notify({ Title = "Skybox", Content = "Custom skybox applied.", Duration = 3 })
    else
        SkyInstance:Destroy()
        Fluent:Notify({ Title = "Skybox", Content = "No valid inputs provided.", Duration = 3 })
    end
end

SecCustomSkybox:AddInput("CustomSkyboxLf", { Title = "Left (Lf)", Placeholder = "rbxassetid or URL", Callback = function(v) CustomSkyboxInputs.Lf = v end })
SecCustomSkybox:AddInput("CustomSkyboxRt", { Title = "Right (Rt)", Placeholder = "rbxassetid or URL", Callback = function(v) CustomSkyboxInputs.Rt = v end })
SecCustomSkybox:AddInput("CustomSkyboxUp", { Title = "Up", Placeholder = "rbxassetid or URL", Callback = function(v) CustomSkyboxInputs.Up = v end })
SecCustomSkybox:AddInput("CustomSkyboxDn", { Title = "Down (Dn)", Placeholder = "rbxassetid or URL", Callback = function(v) CustomSkyboxInputs.Dn = v end })
SecCustomSkybox:AddInput("CustomSkyboxFt", { Title = "Front (Ft)", Placeholder = "rbxassetid or URL", Callback = function(v) CustomSkyboxInputs.Ft = v end })
SecCustomSkybox:AddInput("CustomSkyboxBk", { Title = "Back (Bk)", Placeholder = "rbxassetid or URL", Callback = function(v) CustomSkyboxInputs.Bk = v end })

SecCustomSkybox:AddButton({
    Title = "Apply Custom Skybox",
    Callback = ApplyCustomSkybox
})

SecCustomSkybox:AddParagraph({
    Title = "Custom Skybox Info",
    Content = "Enter an rbxassetid or URL for each of the 6 cube faces to apply a custom skybox."
})

MediaManager:SetFolder("GoonWares/Arsenal")

InterfaceManager:SetLibrary(Fluent)
InterfaceManager:SetFolder("GoonWares/Arsenal/Interface")
InterfaceManager:BuildInterfaceSection(Tabs.Settings)
InterfaceManager:LoadSettings()

SaveManager:SetLibrary(Fluent)
SaveManager:SetFolder("GoonWares/Arsenal/Configuration")
SaveManager:IgnoreThemeSettings()
SaveManager:BuildConfigSection(Tabs.Settings)
SaveManager:LoadAutoloadConfig()

FloatingButtonManager:SetLibrary(Fluent)
FloatingButtonManager:SetFolder("GoonWares/Arsenal/Floatings")
FloatingButtonManager:BuildConfigSection(Tabs.Settings)
FloatingButtonManager:LoadAutoloadConfig()

Notify("GoonWares", "All tabs loaded successfully", "Success", nil, 4)

task.delay(0.5, function()
    Window:SelectTab(1)
end)