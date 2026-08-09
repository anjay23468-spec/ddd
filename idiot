if getgenv().GoonWaresExecuted then
    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = "WARNING!",
        Text = "Script Is Already Loaded, rejoin if you want to re-execute.",
        Duration = 8,
    })
    return
end
getgenv().GoonWaresExecuted = true

Players = game:GetService("Players")
RunService = game:GetService("RunService")
VirtualUser = game:GetService("VirtualUser")
Lighting = game:GetService("Lighting")
ReplicatedStorage = game:GetService("ReplicatedStorage")
workspace = game:GetService("Workspace")
TeleportService = game:GetService("TeleportService")
HttpService = game:GetService("HttpService")
MarketplaceService = game:GetService("MarketplaceService")
CoreGui = game:GetService("CoreGui")
StarterGui = game:GetService("StarterGui")
UserInputService = game:GetService("UserInputService")
PathfindingService = game:GetService("PathfindingService")
LocalPlayer = Players.LocalPlayer
PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

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

isMobile = UserInputService.TouchEnabled and not UserInputService.MouseEnabled and not UserInputService.KeyboardEnabled

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
        { Text = "Hello " .. tostring(LocalPlayer.DisplayName), Color = Color3.fromRGB(211, 20, 10) },
    },
    Version = "MM2/MMV",
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
    ScreenGuiName = "MurderMystery2",
})

Fluent:SetErrorHandler(function(msg, fullErr)
    pcall(function()
        Notify("Error", tostring(msg), "Error", nil, 5)
    end)
end)

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
                            Content = "traps Loserss!",
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

Character = nil
Humanoid = nil
HumanoidRootPart = nil

function setupCharacter(character)
    Character = character
    Humanoid = character:FindFirstChildOfClass("Humanoid")
    HumanoidRootPart = character:FindFirstChild("HumanoidRootPart")
end

if LocalPlayer.Character then
    setupCharacter(LocalPlayer.Character)
end

LocalPlayer.CharacterAdded:Connect(setupCharacter)

CurrentRoundClient = require(ReplicatedStorage:WaitForChild("Modules"):WaitForChild("CurrentRoundClient"))

function GetPlayerData()
    return CurrentRoundClient.GetLatestPlayerData()
end

function GetPlayerRole(playerName)
    local playerData = GetPlayerData()
    if playerData and playerData[playerName] then
        return playerData[playerName].Role
    end
    return nil
end

function GetMurderer()
    local playerData = GetPlayerData()
    if playerData then
        for name, data in pairs(playerData) do
            if data.Role == "Murderer" then
                return Players:FindFirstChild(name)
            end
        end
    end
    return nil
end

function GetSheriff()
    local playerData = GetPlayerData()
    if playerData then
        for name, data in pairs(playerData) do
            if data.Role == "Sheriff" then
                return Players:FindFirstChild(name)
            end
        end
    end
    return nil
end

function GetHero()
    local playerData = GetPlayerData()
    if playerData then
        for name, data in pairs(playerData) do
            if data.Role == "Hero" then
                return Players:FindFirstChild(name)
            end
        end
    end
    return nil
end

function IsPlayerAlive(playerName)
    local playerData = GetPlayerData()
    if playerData and playerData[playerName] then
        local data = playerData[playerName]
        return not (data.Killed or data.Dead)
    end
    return false
end

function IsPlayerDead()
    local playerData = GetPlayerData()
    if playerData and playerData[LocalPlayer.Name] then
        local data = playerData[LocalPlayer.Name]
        return data.Killed or data.Dead
    end
    return false
end

function GetPlayerPerk(playerName)
    local playerData = GetPlayerData()
    if playerData and playerData[playerName] then
        return playerData[playerName].Perk
    end
    return nil
end

function GetMurdererPerk()
    return CurrentRoundClient.GetMurdererPerk()
end
RoleList = {
    Innocent = Color3.fromRGB(0, 255, 0),
    Sheriff = Color3.fromRGB(0, 0, 255),
    Hero = Color3.fromRGB(255, 255, 0),
    Murderer = Color3.fromRGB(255, 0, 0),
    Assassin = Color3.fromRGB(255, 0, 0),
    Zombie = Color3.fromRGB(25, 172, 0),
    Survivor = Color3.fromRGB(43, 154, 238),
    Red = Color3.fromRGB(217, 35, 35),
    Blue = Color3.fromRGB(63, 176, 224),
    Juggernaut = Color3.fromRGB(217, 35, 35),
    Gladiator = Color3.fromRGB(63, 176, 224),
    Freezer = Color3.fromRGB(150, 220, 250),
    Runner = Color3.fromRGB(0, 200, 100)
}
TPWALK = false
TpwalkValue = 1
JumpBoost = false
JumpPower = 5
SpeedHack = false
SpeedValue = 16
Noclip = false

function IsAlive(Player, currentRoles)
    for i, v in pairs(currentRoles) do
        if Player.Name == i then
            if not v.Killed and not v.Dead then
                return true
            else
                return false
            end
        end
    end
    return false
end

function getOutlineColor(c)
    local lum = 0.299 * c.R + 0.587 * c.G + 0.114 * c.B
    if lum > 0.5 then
        return Color3.new(0,0,0)
    else
        return Color3.new(1,1,1)
    end
end

Tabs = {
    Main = Window:AddTab({ Title = "|  Main", Icon = "layout-grid" }),
    Player = Window:AddTab({ Title = "|  Player", Icon = "user" }),
    Combat = Window:AddTab({ Title = "|  Combat", Icon = "swords" }),
    Visuals = Window:AddTab({ Title = "|  Visuals", Icon = "camera" }),
    ESP = Window:AddTab({ Title = "|  Esp", Icon = "eye" }),
    Teleport = Window:AddTab({ Title = "|  Teleport", Icon = "navigation" }),
    Misc = Window:AddTab({ Title = "|  Misc", Icon = "diamond" }),
    Utility = Window:AddTab({ Title = "|  Utility", Icon = "wrench" }),
    Settings = Window:AddTab({ Title = "|  Settings", Icon = "settings" }),
    info = Window:AddTab({ Title = "|  Info", Icon = "info" }),
    Others = Window:AddTab({ Title = "|  Others", Icon = "menu" })
}

do
    local function randFlag(prefix)
        return prefix .. tostring(math.random(100000, 999999))
    end

    local oldMethods = {
        Section = function(self, opts)
            opts = opts or {}
            return self:AddSection(opts.Title or "Section", opts.Icon or "solar/widget-2-bold")
        end,
        Divider = function(self) return self:AddDivider() end,
        Space = function(self) return self:AddSpace({ Height = 20 }) end,
        Paragraph = function(self, opts)
            opts = opts or {}
            return self:AddParagraph({ Title = opts.Title, Content = opts.Content or opts.Desc or opts.Description })
        end,
        Button = function(self, opts)
            opts = opts or {}
            return self:AddButton({ Title = opts.Title or opts.Text, Icon = opts.Icon, Description = opts.Desc or opts.Description, Callback = opts.Callback })
        end,
        Toggle = function(self, opts)
            opts = opts or {}
            local flag = opts.Flag or randFlag("Toggle")
            local default = opts.Default
            if default == nil then default = opts.Value end
            return self:AddToggle(flag, { Title = opts.Title or opts.Text, Icon = opts.Icon, Description = opts.Desc or opts.Description, Default = default or false, Callback = opts.Callback })
        end,
        Input = function(self, opts)
            opts = opts or {}
            local flag = opts.Flag or randFlag("Input")
            return self:AddInput(flag, { Title = opts.Title, Icon = opts.Icon, Placeholder = opts.Placeholder, Default = opts.Default or opts.Value, Numeric = opts.NumbersOnly, Description = opts.Desc or opts.Description, Callback = opts.Callback })
        end,
        Dropdown = function(self, opts)
            opts = opts or {}
            local flag = opts.Flag or randFlag("Dropdown")
            self:AddSpace({ Height = 20 })
            return self:AddDropdown(flag, { Title = opts.Title, Icon = opts.Icon, Values = opts.Values, Default = opts.Default or opts.Value, Description = opts.Desc or opts.Description, Search = false, Callback = opts.Callback })
        end,
        Slider = function(self, opts)
            opts = opts or {}
            local flag = opts.Flag or randFlag("Slider")
            local min, max, default, rounding = opts.Min, opts.Max, opts.Default, opts.Rounding or opts.Step
            if type(opts.Default) == "table" then
                min = opts.Default.Min or min
                max = opts.Default.Max or max
                rounding = opts.Default.Step or rounding
                default = opts.Default.Default
            elseif opts.Value ~= nil then
                default = opts.Value
            end
            return self:AddSlider(flag, { Title = opts.Title, Icon = opts.Icon, Min = min, Max = max, Default = default, Rounding = rounding, Description = opts.Desc or opts.Description, Callback = opts.Callback })
        end,
        Keybind = function(self, opts)
            opts = opts or {}
            local flag = opts.Flag or randFlag("Keybind")
            return self:AddKeybind(flag, { Title = opts.Title, Default = opts.Default or opts.Value, Description = opts.Desc or opts.Description, Callback = opts.Callback })
        end,
        Colorpicker = function(self, opts)
            opts = opts or {}
            local flag = opts.Flag or randFlag("Colorpicker")
            return self:AddColorpicker(flag, { Title = opts.Title, Default = opts.Default or opts.Value, Description = opts.Desc or opts.Description, Callback = opts.Callback })
        end,
        Tab = function(self, opts)
            return Window:AddTab(opts)
        end,
    }

    local function applyShim(obj)
        if not obj then return end
        for name, fn in pairs(oldMethods) do
            if obj[name] == nil then
                obj[name] = fn
            end
        end
    end

    for _, tabObj in pairs(Tabs) do
        applyShim(tabObj)
        local originalAddSection = tabObj.AddSection
        tabObj.AddSection = function(self, ...)
            local section = originalAddSection(self, ...)
            applyShim(section)
            return section
        end
        local originalAddCollapsible = tabObj.AddCollapsibleSection
        if originalAddCollapsible then
            tabObj.AddCollapsibleSection = function(self, ...)
                local section = originalAddCollapsible(self, ...)
                applyShim(section)
                return section
            end
        end
    end
end

function SocialsModule(Tabs)
    local SecDiscord = Tabs.info:AddSection("Discord", "solar/chat-round-bold")

    SecDiscord:AddDiscord({
        InviteCode = "QBhcVu6c",
    })

    SecDiscord:AddDivider()

    Tabs.info:AddSpace({ Height = 20 })
    local SecTikTok = Tabs.info:AddSection("TikTok", "solar/music-note-bold")

    SecTikTok:AddImage({
        Image = "https://unavatar.io/tiktok/styearx",
        AspectRatio = "16:9",
        Radius = 10,
    })

    SecTikTok:AddParagraph({
        Title = "Follow my main account Pls",
        Content = "@styearx",
    })

    SecTikTok:AddCode({
        Title = "",
        Code = "https://www.tiktok.com/@styearx",
        OnCopy = function() Notify("Link", "Copied", "Info", nil, 2) end,
    })

    SecTikTok:AddDivider()

    Tabs.info:AddSpace({ Height = 20 })
    local SecYouTube = Tabs.info:AddSection("YouTube", "solar/videocamera-record-bold")

    SecYouTube:AddImage({
        Image = "https://unavatar.io/youtube/StyearX",
        AspectRatio = "16:9",
        Radius = 10,
    })

    SecYouTube:AddParagraph({
        Title = "Subscribe to my main account :)",
        Content = "@StyearX",
    })

    SecYouTube:AddCode({
        Title = "",
        Code = "https://m.youtube.com/@styearx",
        OnCopy = function() Notify("Link", "Copied", "Info", nil, 2) end,
    })

    SecYouTube:AddDivider()

    Tabs.info:AddSpace({ Height = 20 })
    local SecGithub = Tabs.info:AddSection("GitHub", "solar/code-square-bold")
    SecGithub:AddImage({
        Image = "https://unavatar.io/github/StyearX",
        AspectRatio = "16:9",
        Radius = 10,
    })

    SecGithub:AddParagraph({
        Title = "Check out my GitHub",
        Content = "@StyearX",
    })

    SecGithub:AddCode({
        Title = "",
        Code = "https://github.com/StyearX",
        OnCopy = function() Notify("Link", "Copied", "Info", nil, 2) end,
    })

    SecGithub:AddDivider()

    Tabs.info:AddSpace({ Height = 20 })
    local SecUILibrary = Tabs.info:AddSection("UI Library", "solar/widget-bold")

    SecUILibrary:AddParagraph({
        Title = "Fluent Modded",
        Content = "The UI Library used in this script.",
    })

    SecUILibrary:AddCode({
        Title = "",
        Code = "https://github.com/StyearX/Fluent-modded",
        OnCopy = function() Notify("Link", "Copied", "Info", nil, 2) end,
    })
end
SocialsModule(Tabs)

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

secMainInfo = Tabs.Misc:AddSection("Server Info", "solar/info-circle-bold")

playerCountParagraph = secMainInfo:AddParagraph({
    Title = "Player Count",
    Content = "Waiting..."
})

ModelPlayerAntiBrokenServer = secMainInfo:AddParagraph({
    Title = "Player Model Server Status",
    Content = "Waiting..."
})

playerModelCheckConnection = RunService.Heartbeat:Connect(function()
    local players = Players:GetPlayers()
    local playerCount = #players
    local modelCount = 0

    for _, player in ipairs(players) do
        if player.Character then
            modelCount = modelCount + 1
        end
    end

    playerCountParagraph:SetDesc(playerCount .. " Online | Player Models Found: " .. modelCount)

    if playerCount == modelCount and playerCount > 0 then
        ModelPlayerAntiBrokenServer:SetDesc("Player Model Is Correct Definitely Playable")
    else
        ModelPlayerAntiBrokenServer:SetDesc("Unplayable Server Detected! Missing Player Model, Find a new server")
    end
end)

UniverseServerTools(Tabs)

local FloatingButtonModule = loadstring(game:HttpGet("https://raw.githubusercontent.com/StyearX/GoonWares/refs/heads/main/Module/FloatingButton.lua"))()
FBModule = FloatingButtonModule.new(Fluent, FloatingButtonManager)

secPlayerAuto1 = Tabs.Player:AddSection("Player", "solar/widget-2-bold")
secPlayerAuto1:AddDivider()

function onCharacterAdded(newCharacter)
    setupCharacter(newCharacter)
    if JumpBoost and Humanoid then
        Humanoid.JumpPower = JumpPower
        Humanoid.JumpHeight = JumpPower
        setupJumpBoost()
    end
    if SpeedHack and Humanoid then
        Humanoid.WalkSpeed = SpeedValue
    end
end

LocalPlayer.CharacterAdded:Connect(onCharacterAdded)
if LocalPlayer.Character then
    onCharacterAdded(LocalPlayer.Character)
end
InfiniteJump = {
    State = nil,
    Connection = nil,
    Enabled = false
}

function StartInfiniteJump()
    if InfiniteJump.Enabled then return end
    InfiniteJump.Enabled = true
    InfiniteJump.Connection = RunService.RenderStepped:Connect(function()
        if not InfiniteJump.Enabled then return end
        if not Humanoid then
            if LocalPlayer.Character then
                Humanoid = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
            end
            if not Humanoid then return end
        end
        if Humanoid.Jump then
            if InfiniteJump.State then
                Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
                InfiniteJump.State = false
            end
        else
            InfiniteJump.State = true
        end
    end)
end

function StopInfiniteJump()
    InfiniteJump.Enabled = false
    if InfiniteJump.Connection then
        InfiniteJump.Connection:Disconnect()
        InfiniteJump.Connection = nil
    end
    InfiniteJump.State = nil
end


InfiniteJumpToggle = secPlayerAuto1:AddToggle("InfiniteJumpToggle", {
    Title = "Infinite Jump",
    Default = false,
    Callback = function(DConfiguration)
        if DConfiguration then
        StartInfiniteJump()
        else
        StopInfiniteJump()
        end
    end
})


SpeedToggle = secPlayerAuto1:AddToggle("SpeedToggle", {
    Title = "Speed Hack",
    Default = SpeedHack,
    Callback = function(DConfiguration)
        SpeedHack = DConfiguration
        if DConfiguration and Humanoid then
            Humanoid.WalkSpeed = SpeedValue
        elseif Humanoid then
            Humanoid.WalkSpeed = 16
        end
    end
})

SpeedSlider = secPlayerAuto1:AddSlider("SpeedSlider", {
    Title = "Speed Value",
    Description = "Adjust walk speed",
    Min = 16,
    Max = 200,
    Default = SpeedValue,
    Rounding = 1,
    Callback = function(value)
        SpeedValue = value
        if SpeedHack and Humanoid then
            Humanoid.WalkSpeed = value
        end
    end
})

SpeedGlitchMode = "Air Acceleration"
SpeedGlitchEnabled = false
SpeedGlitchSpeed = 50
speedGlitchCurrentSpeed = 0
speedGlitchWasMoving = false
speedGlitchConnection = nil
wasOnGround = false
realisticHolder = nil
currentCharacter = nil
currentRoot = nil
currentHumanoid = nil

function applyAirAccelerationGlitch(character, humanoid, rootPart)
    local moveDir = humanoid.MoveDirection
    if moveDir.Magnitude > 0 then
        speedGlitchCurrentSpeed = speedGlitchCurrentSpeed + (SpeedGlitchSpeed * 0.1)
        local velocity = moveDir * speedGlitchCurrentSpeed
        rootPart.Velocity = Vector3.new(velocity.X, rootPart.Velocity.Y, velocity.Z)
        speedGlitchWasMoving = true
    else
        speedGlitchWasMoving = false
    end
end

function recreateRealisticHolder(character, rootPart)
    if realisticHolder then
        realisticHolder:Destroy()
        realisticHolder = nil
    end

    if not character or not rootPart then return end

    local ws = SpeedGlitchSpeed
    local holder = Instance.new("Part")
    holder.Size = Vector3.new(2, 2, 2)
    holder.Anchored = false
    holder.CanCollide = false
    holder.Transparency = 1
    holder.CFrame = rootPart.CFrame * CFrame.new(10 + (ws * 0.5), 10, -ws)
    holder.Name = "PhysicHolder"
    holder.Parent = character

    local ActualWeld = Instance.new("WeldConstraint")
    ActualWeld.Part0 = rootPart
    ActualWeld.Part1 = holder
    ActualWeld.Parent = rootPart

    realisticHolder = holder
end

function applyRealisticGlitch(character, humanoid, rootPart)
    if not character or not rootPart then return end
    if not realisticHolder or not realisticHolder.Parent or realisticHolder.Parent ~= character then
        recreateRealisticHolder(character, rootPart)
    end
end

function startSpeedGlitch(character, humanoid, rootPart)
    if not character or not humanoid or not rootPart then return end

    currentCharacter = character
    currentRoot = rootPart
    currentHumanoid = humanoid

    if speedGlitchConnection then
        speedGlitchConnection:Disconnect()
        speedGlitchConnection = nil
    end

    speedGlitchConnection = RunService.Heartbeat:Connect(function()
        if SpeedGlitchEnabled and currentHumanoid and currentHumanoid.Parent then
            local isOnGround = currentHumanoid.FloorMaterial ~= Enum.Material.Air
            local isMoving = currentHumanoid.MoveDirection.Magnitude > 0

            if isOnGround and wasOnGround and not isMoving then
                speedGlitchCurrentSpeed = 0
                speedGlitchWasMoving = false
                if currentRoot then
                    local currentVel = currentRoot.Velocity
                    currentRoot.Velocity = Vector3.new(currentVel.X * 0.95, currentVel.Y, currentVel.Z * 0.95)
                end
            end

            wasOnGround = isOnGround

            if currentHumanoid.FloorMaterial == Enum.Material.Air and currentHumanoid:GetState() ~= Enum.HumanoidStateType.Climbing and currentHumanoid:GetState() ~= Enum.HumanoidStateType.Swimming and currentHumanoid:GetState() ~= Enum.HumanoidStateType.Seated and currentHumanoid:GetState() ~= Enum.HumanoidStateType.PlatformStanding then
                if SpeedGlitchMode == "Air Acceleration" then
                    applyAirAccelerationGlitch(currentCharacter, currentHumanoid, currentRoot)
                elseif SpeedGlitchMode == "Realistic" then
                    applyRealisticGlitch(currentCharacter, currentHumanoid, currentRoot)
                end
            end
        end
    end)
end

function stopSpeedGlitch()
    if speedGlitchConnection then
        speedGlitchConnection:Disconnect()
        speedGlitchConnection = nil
    end
    speedGlitchCurrentSpeed = 0
    speedGlitchWasMoving = false
    wasOnGround = false
    if realisticHolder then
        realisticHolder:Destroy()
        realisticHolder = nil
    end
end

function updateSpeedValue()
    if SpeedGlitchEnabled and SpeedGlitchMode == "Realistic" and currentCharacter and currentRoot then
        recreateRealisticHolder(currentCharacter, currentRoot)
    end
end

function onCharacterAdded(character)
    task.wait(0.5)
    if SpeedGlitchEnabled then
        speedGlitchCurrentSpeed = 0
        local hum = character:FindFirstChildOfClass("Humanoid")
        local root = character:FindFirstChild("HumanoidRootPart")
        if hum and root then
            stopSpeedGlitch()
            startSpeedGlitch(character, hum, root)
        end
    end
end

function onCharacterRemoving()
    stopSpeedGlitch()
    currentCharacter = nil
    currentRoot = nil
    currentHumanoid = nil
end

secPlayerAuto1:AddSpace({ Height = 20 })
secPlayerAuto1:AddDivider()
Tabs.Player:AddSpace({ Height = 20 })
secPlayerAuto2 = Tabs.Player:AddSection("Speed Glitch", "solar/widget-2-bold")
secPlayerAuto2:AddDivider()

SpeedGlitchToggle = secPlayerAuto2:AddToggle("SpeedGlitchToggle", {
    Title = "Speed Glitch",
    Default = false,
    Callback = function(DConfiguration)
        SpeedGlitchEnabled = DConfiguration
        if DConfiguration then
            speedGlitchCurrentSpeed = 0
            local char = LocalPlayer.Character
            if char then
                local hum = char:FindFirstChildOfClass("Humanoid")
                local root = char:FindFirstChild("HumanoidRootPart")
                if hum and root then
                    startSpeedGlitch(char, hum, root)
                end
            end
            if SpeedGlitchFrame then
                FloatingButtonModule.SetActive(SpeedGlitchButton, true, "Speed Glitch")
            end
        else
            stopSpeedGlitch()
            if SpeedGlitchFrame then
                FloatingButtonModule.SetActive(SpeedGlitchButton, false, "Speed Glitch")
            end
        end
    end
})

SpeedGlitchModeDropdown = secPlayerAuto2:AddDropdown("SpeedGlitchModeDropdown", {
    Search = false,
    Title = "Speed Glitch Mode",
    Values = {"Air Acceleration", "Realistic"},
    Default = "Air Acceleration",
    Callback = function(value)
        SpeedGlitchMode = value
        if SpeedGlitchEnabled then
            local char = LocalPlayer.Character
            if char then
                local hum = char:FindFirstChildOfClass("Humanoid")
                local root = char:FindFirstChild("HumanoidRootPart")
                if hum and root then
                    stopSpeedGlitch()
                    startSpeedGlitch(char, hum, root)
                end
            end
        end
        if SpeedGlitchMode == "Realistic" and SpeedGlitchEnabled then
            updateSpeedValue()
        end
    end
})

SpeedGlitchSpeedInput = secPlayerAuto2:AddInput("SpeedGlitchSpeedInput", {
    Title = "Speed Value",
    Placeholder = "50",
    Default = "50",
    Numeric = true,
    Callback = function(value)
        local num = tonumber(value)
        if num and num > 0 then
            SpeedGlitchSpeed = num
            updateSpeedValue()
        end
    end
})

SpeedGlitchFrame, SpeedGlitchButton = FBModule:Create("SpeedGlitchBtn", "Speed Glitch", true, function(Btn)
    if SpeedGlitchToggle then
        local newState = not SpeedGlitchEnabled
        SpeedGlitchToggle:SetValue(newState)
        FloatingButtonModule.SetActive(Btn, newState, "Speed Glitch")
    end
end)

secPlayerAuto2:AddSpace({ Height = 20 })
ShowSpeedGlitchButtonToggle = secPlayerAuto2:AddToggle("ShowSpeedGlitchButtonToggle", {
    Title = "Show Speed Glitch Button",
    Default = false,
    Callback = function(DConfiguration)
        FloatingButtonModule.SetVisible(SpeedGlitchFrame, DConfiguration)
    end,
})

SpeedGlitchKeybind = secPlayerAuto2:AddKeybind("SpeedGlitchKeybind", {
    Title = "Speed Glitch Keybind",
    Description = "Toggle Speed Glitch",
    Default = "",
    Callback = function()
        if SpeedGlitchToggle then SpeedGlitchToggle:SetValue(not SpeedGlitchEnabled) end
    end,
})

FBModule:AddSizeInputs(secPlayerAuto2, "SpeedGlitchBtn", "Speed Glitch")

if LocalPlayer.Character then
    onCharacterAdded(LocalPlayer.Character)
end

LocalPlayer.CharacterAdded:Connect(onCharacterAdded)
LocalPlayer.CharacterRemoving:Connect(onCharacterRemoving)

Noclip = nil
Clip = nil

function noclip()
    Clip = false
    function Nocl()
        if Clip == false and LocalPlayer.Character ~= nil then
            for _,v in pairs(LocalPlayer.Character:GetDescendants()) do
                if v:IsA('BasePart') and v.CanCollide then
                    v.CanCollide = false
                end
            end
        end
        wait(0.21)
    end
    Noclip = RunService.Stepped:Connect(Nocl)
end

function clip()
    if Noclip then 
        Noclip:Disconnect() 
    end
    Clip = true
    if LocalPlayer.Character then
        for _,v in pairs(LocalPlayer.Character:GetDescendants()) do
            if v:IsA('BasePart') then
                v.CanCollide = true
            end
        end
    end
end

NoclipToggle = secPlayerAuto2:AddToggle("NoclipToggle", {
    Title = "Noclip",
    Default = Noclip,
    Callback = function(DConfiguration)
        Noclip = DConfiguration
        if DConfiguration then
            noclip()
        else
            clip()
        end
    end
})

IsOnMobile = false
xpcall(function()
    IsOnMobile = table.find({Enum.Platform.Android, Enum.Platform.IOS}, UserInputService:GetPlatform()) ~= nil
end, function()
    IsOnMobile = UserInputService.TouchEnabled and not UserInputService.KeyboardEnabled
end)

if IsOnMobile then
    LocalPlayer:WaitForChild("PlayerGui")
    local touchGui = PlayerGui:WaitForChild("TouchGui")
    local touchControlFrame = touchGui:WaitForChild("TouchControlFrame")
    local originalJumpButton = touchControlFrame:WaitForChild("JumpButton")
    local DownWardJumpBtn = nil

    function createDownwardButton()
        if DownWardJumpBtn and DownWardJumpBtn.Parent then
            DownWardJumpBtn:Destroy()
        end

        DownWardJumpBtn = Instance.new("ImageButton")
        DownWardJumpBtn.Name = "DownWardJumpBtn"
        DownWardJumpBtn.Size = originalJumpButton.Size
        DownWardJumpBtn.Image = originalJumpButton.Image
        DownWardJumpBtn.ImageRectOffset = originalJumpButton.ImageRectOffset
        DownWardJumpBtn.ImageRectSize = originalJumpButton.ImageRectSize
        DownWardJumpBtn.BackgroundTransparency = 1
        DownWardJumpBtn.AnchorPoint = Vector2.new(1, 0)
        DownWardJumpBtn.AutoButtonColor = false
        DownWardJumpBtn.Position = UDim2.new(1, 0, originalJumpButton.Position.Y.Scale, originalJumpButton.Position.Y.Offset)
        DownWardJumpBtn.Rotation = 180

        local originalRectOffset = originalJumpButton.ImageRectOffset
        local isHoldingDown = false

        DownWardJumpBtn.MouseButton1Down:Connect(function()
            isHoldingDown = true
            DownWardJumpBtn.ImageRectOffset = Vector2.new(146, 146)
            flyDownPressed = true
        end)

        DownWardJumpBtn.MouseButton1Up:Connect(function()
            if isHoldingDown then
                isHoldingDown = false
                DownWardJumpBtn.ImageRectOffset = originalRectOffset
                flyDownPressed = false
            end
        end)

        DownWardJumpBtn.MouseLeave:Connect(function()
            if isHoldingDown then
                isHoldingDown = false
                DownWardJumpBtn.ImageRectOffset = originalRectOffset
                flyDownPressed = false
            end
        end)

        DownWardJumpBtn.Parent = touchControlFrame

        function preventOverlap()
            if not DownWardJumpBtn or not DownWardJumpBtn.Parent then return end
            local buttonWidth = DownWardJumpBtn.AbsoluteSize.X
            local originalButton = touchControlFrame:FindFirstChild("JumpButton")

            if originalButton then
                local originalRightEdge = originalButton.AbsolutePosition.X + originalButton.AbsoluteSize.X
                local duplicateLeftEdge = DownWardJumpBtn.AbsolutePosition.X
                local distance = duplicateLeftEdge - originalRightEdge

                if distance < 1 then
                    local neededOffset = 1 - distance
                    local newXOffset = DownWardJumpBtn.Position.X.Offset - neededOffset
                    DownWardJumpBtn.Position = UDim2.new(1, newXOffset, DownWardJumpBtn.Position.Y.Scale, DownWardJumpBtn.Position.Y.Offset)
                elseif distance > 1 then
                    local neededOffset = distance - 1
                    local newXOffset = DownWardJumpBtn.Position.X.Offset + neededOffset
                    DownWardJumpBtn.Position = UDim2.new(1, newXOffset, DownWardJumpBtn.Position.Y.Scale, DownWardJumpBtn.Position.Y.Offset)
                end
            end
        end

        DownWardJumpBtn:GetPropertyChangedSignal("AbsoluteSize"):Connect(preventOverlap)
        workspace.CurrentCamera:GetPropertyChangedSignal("ViewportSize"):Connect(preventOverlap)
        preventOverlap()
    end

    local isHoldingJump = false
    local originalJumpRectOffset = originalJumpButton.ImageRectOffset

    originalJumpButton.MouseButton1Down:Connect(function()
        isHoldingJump = true
        originalJumpButton.ImageRectOffset = Vector2.new(146, 146)
        flyUpPressed = true
    end)

    originalJumpButton.MouseButton1Up:Connect(function()
        if isHoldingJump then
            isHoldingJump = false
            originalJumpButton.ImageRectOffset = originalJumpRectOffset
            flyUpPressed = false
        end
    end)

    originalJumpButton.MouseLeave:Connect(function()
        if isHoldingJump then
            isHoldingJump = false
            originalJumpButton.ImageRectOffset = originalJumpRectOffset
            flyUpPressed = false
        end
    end)
end

FLYING = false
flyspeed = 5
flyKeyDown = nil
flyKeyUp = nil
flyVelocityHandlerName = "FlyVelocity_" .. math.random(1000, 9999)
flyGyroHandlerName = "FlyGyro_" .. math.random(1000, 9999)
mfly1 = nil
mfly2 = nil
flyUpPressed = false
flyDownPressed = false

function getRoot(character)
    return character and (character:FindFirstChild("HumanoidRootPart") or character:FindFirstChild("Torso") or character:FindFirstChild("UpperTorso"))
end

function unmobilefly(speaker)
    pcall(function()
        FLYING = false
        flyUpPressed = false
        flyDownPressed = false
        local root = getRoot(speaker.Character)
        if root then
            local bv = root:FindFirstChild(flyVelocityHandlerName)
            local bg = root:FindFirstChild(flyGyroHandlerName)
            if bv then bv:Destroy() end
            if bg then bg:Destroy() end
        end
        if speaker.Character and speaker.Character:FindFirstChildWhichIsA("Humanoid") then
            speaker.Character:FindFirstChildWhichIsA("Humanoid").PlatformStand = false
        end
        if mfly1 then mfly1:Disconnect() mfly1 = nil end
        if mfly2 then mfly2:Disconnect() mfly2 = nil end

        if DownWardJumpBtn and DownWardJumpBtn.Parent then
            DownWardJumpBtn:Destroy()
            DownWardJumpBtn = nil
        end
    end)
end

function mobilefly(speaker)
    unmobilefly(speaker)
    FLYING = true
    createDownwardButton()

    local root = getRoot(speaker.Character)
    if not root then return end

    local camera = workspace.CurrentCamera
    local v3none = Vector3.new()
    local v3zero = Vector3.new(0, 0, 0)
    local v3inf = Vector3.new(9e9, 9e9, 9e9)

    local controlModule = nil
    pcall(function()
        controlModule = require(speaker.PlayerScripts:WaitForChild("PlayerModule"):WaitForChild("ControlModule"))
    end)

    local bv = Instance.new("BodyVelocity")
    bv.Name = flyVelocityHandlerName
    bv.Parent = root
    bv.MaxForce = v3zero
    bv.Velocity = v3zero

    local bg = Instance.new("BodyGyro")
    bg.Name = flyGyroHandlerName
    bg.Parent = root
    bg.MaxTorque = v3inf
    bg.P = 1000
    bg.D = 50

    mfly2 = RunService.RenderStepped:Connect(function()
        local currentRoot = getRoot(speaker.Character)
        local currentCamera = workspace.CurrentCamera
        local currentHumanoid = speaker.Character and speaker.Character:FindFirstChildWhichIsA("Humanoid")

        if currentHumanoid and currentRoot and currentRoot:FindFirstChild(flyVelocityHandlerName) and currentRoot:FindFirstChild(flyGyroHandlerName) then
            local VelocityHandler = currentRoot:FindFirstChild(flyVelocityHandlerName)
            local GyroHandler = currentRoot:FindFirstChild(flyGyroHandlerName)

            VelocityHandler.MaxForce = v3inf
            GyroHandler.MaxTorque = v3inf
            currentHumanoid.PlatformStand = true
            GyroHandler.CFrame = currentCamera.CoordinateFrame

            local moveVector = Vector3.new(0, 0, 0)

            if controlModule then
                local direction = controlModule:GetMoveVector()
                local speed = flyspeed * 50

                moveVector = (currentCamera.CFrame.RightVector * direction.X * speed) +
                             (-currentCamera.CFrame.LookVector * direction.Z * speed)
            end

            if flyUpPressed then
                moveVector = moveVector + Vector3.new(0, flyspeed * 50, 0)
            end
            if flyDownPressed then
                moveVector = moveVector - Vector3.new(0, flyspeed * 50, 0)
            end

            VelocityHandler.Velocity = moveVector
        end
    end)
end

function pcfly()
    local plr = LocalPlayer
    local char = plr.Character or plr.CharacterAdded:Wait()
    local humanoid = char:FindFirstChildOfClass("Humanoid")
    if not humanoid then
        repeat task.wait() until char:FindFirstChildOfClass("Humanoid")
        humanoid = char:FindFirstChildOfClass("Humanoid")
    end

    if flyKeyDown or flyKeyUp then
        flyKeyDown:Disconnect()
        flyKeyUp:Disconnect()
    end

    local T = getRoot(char)
    if not T then return end

    local WPressed = false
    local SPressed = false
    local APressed = false
    local DPressed = false
    local SpacePressed = false
    local CtrlPressed = false

    function FLY()
        FLYING = true
        local BG = Instance.new('BodyGyro')
        local BV = Instance.new('BodyVelocity')
        BG.P = 9e4
        BG.Parent = T
        BV.Parent = T
        BG.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
        BG.CFrame = T.CFrame
        BV.Velocity = Vector3.new(0, 0, 0)
        BV.MaxForce = Vector3.new(9e9, 9e9, 9e9)

        task.spawn(function()
            while FLYING do
                task.wait()
                local camera = workspace.CurrentCamera
                humanoid.PlatformStand = true

                local moveDirection = Vector3.new(0, 0, 0)

                if WPressed then
                    moveDirection = moveDirection + camera.CFrame.LookVector * flyspeed
                end
                if SPressed then
                    moveDirection = moveDirection - camera.CFrame.LookVector * flyspeed
                end
                if APressed then
                    moveDirection = moveDirection - camera.CFrame.RightVector * flyspeed
                end
                if DPressed then
                    moveDirection = moveDirection + camera.CFrame.RightVector * flyspeed
                end
                if SpacePressed then
                    moveDirection = moveDirection + Vector3.new(0, flyspeed * 2, 0)
                end
                if CtrlPressed then
                    moveDirection = moveDirection - Vector3.new(0, flyspeed * 2, 0)
                end

                BV.Velocity = moveDirection * 16
                BG.CFrame = camera.CFrame
            end

            BG:Destroy()
            BV:Destroy()
            if humanoid then humanoid.PlatformStand = false end
        end)
    end

    flyKeyDown = UserInputService.InputBegan:Connect(function(input, processed)
        if processed then return end
        if input.KeyCode == Enum.KeyCode.W then
            WPressed = true
        elseif input.KeyCode == Enum.KeyCode.S then
            SPressed = true
        elseif input.KeyCode == Enum.KeyCode.A then
            APressed = true
        elseif input.KeyCode == Enum.KeyCode.D then
            DPressed = true
        elseif input.KeyCode == Enum.KeyCode.Space then
            SpacePressed = true
        elseif input.KeyCode == Enum.KeyCode.LeftControl then
            CtrlPressed = true
        end
        pcall(function() workspace.CurrentCamera.CameraType = Enum.CameraType.Track end)
    end)

    flyKeyUp = UserInputService.InputEnded:Connect(function(input, processed)
        if processed then return end
        if input.KeyCode == Enum.KeyCode.W then
            WPressed = false
        elseif input.KeyCode == Enum.KeyCode.S then
            SPressed = false
        elseif input.KeyCode == Enum.KeyCode.A then
            APressed = false
        elseif input.KeyCode == Enum.KeyCode.D then
            DPressed = false
        elseif input.KeyCode == Enum.KeyCode.Space then
            SpacePressed = false
        elseif input.KeyCode == Enum.KeyCode.LeftControl then
            CtrlPressed = false
        end
    end)

    FLY()
end

function NOFLY()
    FLYING = false
    flyUpPressed = false
    flyDownPressed = false
    if flyKeyDown then 
        flyKeyDown:Disconnect()
        flyKeyDown = nil
    end
    if flyKeyUp then 
        flyKeyUp:Disconnect()
        flyKeyUp = nil
    end

    if IsOnMobile then
        unmobilefly(LocalPlayer)
    else
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass('Humanoid') then
            LocalPlayer.Character:FindFirstChildOfClass('Humanoid').PlatformStand = false
        end
        local root = getRoot(LocalPlayer.Character)
        if root then
            root.Velocity = Vector3.new(0, 0, 0)
        end
    end
    pcall(function() workspace.CurrentCamera.CameraType = Enum.CameraType.Custom end)
end

function onCharacterAdded()
    if FlyToggle and FlyToggle.Value then
        task.wait(1)
        if IsOnMobile then
            mobilefly(LocalPlayer)
        else
            pcfly()
        end
    end
end

LocalPlayer.CharacterAdded:Connect(function()
    NOFLY()
    onCharacterAdded()
end)


FlyToggle = secPlayerAuto2:AddToggle("FlyToggle", {
    Title = "Fly",
    Default = false,
    Callback = function(DConfiguration)
        if DConfiguration then
            if IsOnMobile then
                mobilefly(LocalPlayer)
            else
                pcfly()
            end
        else
            NOFLY()
        end
    end
})

FlySpeedInput = secPlayerAuto2:AddInput("FlySpeedInput", {
    Title = "Fly Speed",
    Placeholder = "Enter speed value",
    Default = tostring(flyspeed),
    Numeric = true,
    Callback = function(value)
        local speed = tonumber(value)
        if speed and speed > 0 then
            flyspeed = speed
        end
    end
})

secPlayerAuto2:AddSpace({ Height = 20 })
ShowFlyButtonToggle = secPlayerAuto2:AddToggle("ShowFlyButton", {
    Title = "Fly Button",
    Default = false,
    Callback = function(DConfiguration)
        FloatingButtonModule.SetVisible(FlightFrame, DConfiguration)
    end,
})

FlyTogglekeybind = secPlayerAuto2:AddKeybind("FlyTogglekeybind", {
    Title = "Fly Keybind",
    Description = "Toggle Fly",
    Default = "",
    Callback = function()
        if FlyToggle then FlyToggle:SetValue(not FlyToggle.Value) end
    end,
})

FBModule:AddSizeInputs(secPlayerAuto2, "FlightBtn", "Flight")

FlightFrame, FlightButton = FBModule:Create("FlightBtn", "Flight", true, function(Btn)
    if FlyToggle then
        local newState = not FlyToggle.Value
        FlyToggle:SetValue(newState)
        FloatingButtonModule.SetActive(Btn, newState, "Flight")
    end
end)

godModeEnabled = false
godModeConnection = nil
godModeMethod = "Health Math.huge"

function applyHumanoidReplacement()
    local Char = LocalPlayer.Character
    local Human = Char and Char:FindFirstChildWhichIsA("Humanoid")
    if not Human then return end

    local nHuman = Human:Clone()
    nHuman.Parent = Char
    LocalPlayer.Character = nil
    nHuman:SetStateEnabled(Enum.HumanoidStateType.FallingDown, false)
    nHuman:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, false)
    nHuman:SetStateEnabled(Enum.HumanoidStateType.Physics, false)
    nHuman.BreakJointsOnDeath = true
    nHuman.MaxHealth = math.huge
    nHuman.Health = math.huge
    Human:Destroy()
    LocalPlayer.Character = Char
    nHuman.DisplayDistanceType = Enum.HumanoidDisplayDistanceType.None

    local Script = Char:FindFirstChild("Animate")
    if Script then
        Script.Disabled = true
        wait()
        Script.Disabled = false
    end
end

function applyHealthMathHuge()
    local Char = LocalPlayer.Character
    local Human = Char and Char:FindFirstChildWhichIsA("Humanoid")
    if not Human then return end

    Human.MaxHealth = math.huge
    Human.Health = math.huge

    Human:GetPropertyChangedSignal("Health"):Connect(function()
        if godModeEnabled and Human.Health < Human.MaxHealth then
            Human.Health = Human.MaxHealth
        end
    end)
end

function applyGodMode()
    if godModeMethod == "Humanoid Replacement (Very buggy)" then
        applyHumanoidReplacement()
    elseif godModeMethod == "Health Math.huge" then
        applyHealthMathHuge()
    end
end

function startGodMode()
    if godModeConnection then return end

    godModeConnection = RunService.Heartbeat:Connect(function()
        if godModeEnabled and LocalPlayer.Character then
            local Human = LocalPlayer.Character:FindFirstChildWhichIsA("Humanoid")
            if Human and Human.Health < math.huge then
                applyGodMode()
            end
        end
    end)
end

function stopGodMode()
    if godModeConnection then
        godModeConnection:Disconnect()
        godModeConnection = nil
    end
end

GodModeToggle = secPlayerAuto2:AddToggle("GodModeToggle", {
    Title = "God Mode",
    Description = "Become invincible",
    Default = false,
    Callback = function(DConfiguration)
        godModeEnabled = DConfiguration
        if DConfiguration then
            applyGodMode()
            startGodMode()
        else
            stopGodMode()
        end
    end
})

GodModeMethodDropdown = secPlayerAuto2:AddDropdown("GodModeMethodDropdown", {
    Search = false,
    Title = "God Mode Method",
    Values = {"Health Math.huge", "Humanoid Replacement (Very buggy)"},
    Default = "Health Math.huge",
    MenuWidth = 400,
    Callback = function(value)
        godModeMethod = value
        if godModeEnabled then
            applyGodMode()
        end
    end
})

ToggleTpwalk = false
TpwalkConnection = nil

function Tpwalking()
    if ToggleTpwalk and Character and Humanoid and HumanoidRootPart then
        local moveDirection = Humanoid.MoveDirection
        local moveDistance = TpwalkValue
        local origin = HumanoidRootPart.Position
        local direction = moveDirection * moveDistance
        local targetPosition = origin + direction
        local raycastParams = RaycastParams.new()
        raycastParams.FilterDescendantsInstances = {Character}
        raycastParams.FilterType = Enum.RaycastFilterType.Exclude
        local raycastResult = workspace:Raycast(origin, direction, raycastParams)
        if raycastResult then
            local hitPosition = raycastResult.Position
            local distanceToHit = (hitPosition - origin).Magnitude
            if distanceToHit < math.abs(moveDistance) then
                targetPosition = origin + (direction.Unit * (distanceToHit - 0.1))
            end
        end
        HumanoidRootPart.CFrame = CFrame.new(targetPosition) * HumanoidRootPart.CFrame.Rotation
        HumanoidRootPart.CanCollide = true
    end
end

function startTpwalk()
    ToggleTpwalk = true
    if TpwalkConnection then
        TpwalkConnection:Disconnect()
    end
    TpwalkConnection = RunService.Heartbeat:Connect(Tpwalking)
end

function stopTpwalk()
    ToggleTpwalk = false
    if TpwalkConnection then
        TpwalkConnection:Disconnect()
        TpwalkConnection = nil
    end
    if HumanoidRootPart then
        HumanoidRootPart.CanCollide = false
    end
end

TPWALKToggle = secPlayerAuto2:AddToggle("TPWALKToggle", {
    Title = "TP WALK",
    Default = TPWALK,
    Callback = function(DConfiguration)
        TPWALK = DConfiguration
        if DConfiguration then
            startTpwalk()
        else
            stopTpwalk()
        end
    end
})

secPlayerAuto2:AddSpace({ Height = 20 })
TPWALKSlider = secPlayerAuto2:AddSlider("TPWALKSlider", {
    Title = "TPWALK VALUE",
    Description = "Adjust TPWALK speed",
    Min = 1,
    Max = 200,
    Default = TpwalkValue,
    Rounding = 1,
    Callback = function(value)
        TpwalkValue = value
    end
})

jumpCount = 0
MAX_JUMPS = math.huge

function setupJumpBoost()
    if not Character or not Humanoid then return end
    Humanoid.StateChanged:Connect(function(oldState, newState)
        if newState == Enum.HumanoidStateType.Landed then
            jumpCount = 0
        end
    end)
    Humanoid.Jumping:Connect(function(isJumping)
        if isJumping and JumpBoost and jumpCount < MAX_JUMPS then
            jumpCount = jumpCount + 1
            Humanoid.JumpHeight = JumpPower
            if jumpCount > 1 then
                HumanoidRootPart:ApplyImpulse(Vector3.new(0, JumpPower * HumanoidRootPart.Mass, 0))
            end
        end
    end)
end

function startJumpBoost()
    if Humanoid then
        Humanoid.JumpPower = JumpPower
        Humanoid.JumpHeight = JumpPower
    end
    setupJumpBoost()
end

function stopJumpBoost()
    jumpCount = 0
    if Humanoid then
        Humanoid.JumpPower = 50
        Humanoid.JumpHeight = 50
    end
end

JumpBoostToggle = secPlayerAuto2:AddToggle("JumpBoostToggle", {
    Title = "Jump Height",
    Default = JumpBoost,
    Callback = function(DConfiguration)
        JumpBoost = DConfiguration
        if DConfiguration then
            startJumpBoost()
        else
            stopJumpBoost()
        end
    end
})

JumpBoostSlider = secPlayerAuto2:AddSlider("JumpBoostSlider", {
    Title = "Jump Power",
    Description = "Adjust jump height",
    Min = 1,
    Max = 200,
    Default = JumpPower,
    Rounding = 1,
    Callback = function(value)
        JumpPower = value
        if JumpBoost then
            if Humanoid then
                Humanoid.JumpPower = JumpPower
                Humanoid.JumpHeight = JumpPower
            end
        end
    end
})

secPlayerAuto2:AddButton({
    Title = "Walk on Walls (must reset to stop)",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/randomstring0/Qwerty/refs/heads/main/qwerty21.lua"))()
    end
})

function ApplyFakeDead(v)
    local char = LocalPlayer.Character
    if not char then return end

    local hrp = char:FindFirstChild("HumanoidRootPart")
    local hum = char:FindFirstChildOfClass("Humanoid")

    if v then
        if hrp and hum then
            local animator = hum:FindFirstChild("Animator")
            if animator then
                animator:Destroy()
            end

            hum:ChangeState(Enum.HumanoidStateType.Physics)
            hrp.Anchored = true
            hrp.CFrame = hrp.CFrame * CFrame.Angles(math.rad(90), 0, 0)
            hrp.CFrame = hrp.CFrame + Vector3.new(0, -2, 0)
        end
    else
        if hrp and hum then
            hrp.Anchored = false
            hum:ChangeState(Enum.HumanoidStateType.GettingUp)

            if not hum:FindFirstChild("Animator") then
                local newAnimator = Instance.new("Animator")
                newAnimator.Parent = hum
            end
        end
    end
end

FakeDeadEnabled = false
FakeDeadToggle = secPlayerAuto2:AddToggle("PlayerToggle22", {
    Title = "Fake dead (lays)",
    Compact = true,
    Default = false,
    Callback = function(v)
        FakeDeadEnabled = v
        ApplyFakeDead(v)
        FloatingButtonModule.SetActive(FakeDeadFloatButton, v, "Fake Dead")
    end
})

secPlayerAuto2:AddToggle("FakeDeadBtnVisibleToggle", {
    Title = "Show Fake Dead Button",
    Default = false,
    Callback = function(DConfiguration)
        FloatingButtonModule.SetVisible(FakeDeadFrame, DConfiguration)
    end,
})

FBModule:AddSizeInputs(secPlayerAuto2, "FakeDeadBtn", "Fake Dead")

FakeDeadFrame, FakeDeadFloatButton = FBModule:Create("FakeDeadBtn", "Fake Dead", true, function(Btn)
    FakeDeadEnabled = not FakeDeadEnabled
    ApplyFakeDead(FakeDeadEnabled)
    FloatingButtonModule.SetActive(Btn, FakeDeadEnabled, "Fake Dead")
    if FakeDeadToggle then
        FakeDeadToggle:SetValue(FakeDeadEnabled)
    end
end)

cameraInputModule = nil
cameraLockEnabled = false
lockedTarget = nil
cameraLockConnection = nil

AimbotEnabled = false
ShowFOV = false
FOVThickness = 2
FOVColor = Color3.fromRGB(0, 120, 255)
targetTypes = {}
aimPart = "Head"
aimLockType = "Realistic"
smoothnessValue = 10
wallCheckEnabled = false
fovRadius = 100
lockFOVToCenter = true
maxDistance = 1000
FOVCircleFrame = nil
FOVCircleGui = nil
FOVCircleStroke = nil
FOVCircleFillGradient = nil
FOVCircleStrokeGradient = nil
FOVCircleRotationTask = nil
aimbotRenderConnection = nil
aimbotRunning = false
aimbotConnection = nil

roleTargets = {}
roleCacheTime = 0
ROLE_CACHE_DURATION = 0.5

function IsOtherPlayerDead(player)
    local playerData = GetPlayerData()
    if playerData and playerData[player.Name] then
        local data = playerData[player.Name]
        return data.Killed or data.Dead
    end
    return false
end

function getRoleListValues()
    local roles = {}
    for roleName, _ in pairs(RoleList) do
        table.insert(roles, roleName)
    end
    table.sort(roles)
    return roles
end

function updateRoleCache()
    local currentTime = tick()
    if currentTime - roleCacheTime < ROLE_CACHE_DURATION then
        return
    end
    roleCacheTime = currentTime
    
    roleTargets = {}
    
    local playerData = GetPlayerData()
    if not playerData then return end
    
    for playerName, data in pairs(playerData) do
        local role = data.Role
        if role and RoleList[role] then
            local player = Players:FindFirstChild(playerName)
            if player and player ~= LocalPlayer and player.Character then
                if not IsOtherPlayerDead(player) then
                    if not roleTargets[role] then
                        roleTargets[role] = {}
                    end
                    local aimPartInstance = getAimPart(player.Character)
                    if aimPartInstance then
                        table.insert(roleTargets[role], {
                            character = player.Character,
                            aimPart = aimPartInstance,
                            player = player,
                            role = role
                        })
                    end
                end
            end
        end
    end
end

function getTargetsByRole(roleName)
    updateRoleCache()
    return roleTargets[roleName] or {}
end

function getAimPart(character)
    if not character then return nil end
    if aimPart == "Head" then
        return character:FindFirstChild("Head")
    elseif aimPart == "Body" then
        return character:FindFirstChild("HumanoidRootPart") or 
            character:FindFirstChild("Torso") or 
            character:FindFirstChild("UpperTorso")
    elseif aimPart == "Legs" then
        return character:FindFirstChild("HumanoidRootPart") or
            character:FindFirstChild("LowerTorso") or
            character:FindFirstChild("Left Leg") or
            character:FindFirstChild("Right Leg")
    end
    return character:FindFirstChild("Head")
end

function isVisible(part)
    if not wallCheckEnabled or not part then
        return true
    end
    local character = LocalPlayer.Character
    if not character then return false end
    local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
    if not humanoidRootPart then return false end
    
    local origin = humanoidRootPart.Position
    local target = part.Position
    local direction = (target - origin).Unit
    local ray = Ray.new(origin, direction * (target - origin).Magnitude)
    local ignoreList = {character, part.Parent}
    local hit = workspace:FindPartOnRayWithIgnoreList(ray, ignoreList)
    return hit == nil or hit:IsDescendantOf(part.Parent)
end

function getAllTargets()
    local targets = {}
    local character = LocalPlayer.Character
    local playerPos = character and character:FindFirstChild("HumanoidRootPart") and character.HumanoidRootPart.Position or nil
    
    if #targetTypes == 0 then
        local allRoles = getRoleListValues()
        for _, roleName in ipairs(allRoles) do
            local roleTargetsList = getTargetsByRole(roleName)
            for _, target in ipairs(roleTargetsList) do
                if target.aimPart then
                    if not playerPos or (target.aimPart.Position - playerPos).Magnitude <= maxDistance then
                        table.insert(targets, target)
                    end
                end
            end
        end
    else
        for _, targetType in ipairs(targetTypes) do
            local roleTargetsList = getTargetsByRole(targetType)
            for _, target in ipairs(roleTargetsList) do
                if target.aimPart then
                    if not playerPos or (target.aimPart.Position - playerPos).Magnitude <= maxDistance then
                        table.insert(targets, target)
                    end
                end
            end
        end
    end
    return targets
end

function isValidTarget(target)
    if not target or not target.aimPart then
        return false
    end
    local character = LocalPlayer.Character
    if not character then return false end
    local humanoid = character:FindFirstChildOfClass("Humanoid")
    if not humanoid or humanoid.Health <= 0 then
        return false
    end
    if IsOtherPlayerDead(target.player) then
        return false
    end
    if not target.aimPart.Parent then
        return false
    end
    local distance = (target.aimPart.Position - character.HumanoidRootPart.Position).Magnitude
    if distance > maxDistance then
        return false
    end
    local Camera = workspace.CurrentCamera
    if not Camera then return false end
    local screenPos, onScreen = Camera:WorldToViewportPoint(target.aimPart.Position)
    if not onScreen then
        return false
    end
    local screenCenter = lockFOVToCenter and 
        Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2) or 
        UserInputService:GetMouseLocation()
    local fovDistance = (Vector2.new(screenPos.X, screenPos.Y) - screenCenter).Magnitude
    if fovDistance > fovRadius then
        return false
    end
    if not isVisible(target.aimPart) then
        return false
    end
    return true
end

function getClosestEnemyInFOV()
    local allTargets = getAllTargets()
    if #allTargets == 0 then
        return nil
    end
    
    if lockedTarget and isValidTarget(lockedTarget) then
        return lockedTarget
    end
    
    local closestTarget = nil
    local closestDistance = math.huge
    local Camera = workspace.CurrentCamera
    if not Camera then return nil end
    local screenCenter = lockFOVToCenter and 
        Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2) or 
        UserInputService:GetMouseLocation()
    
    for _, targetData in ipairs(allTargets) do
        local aimPartInstance = targetData.aimPart
        if aimPartInstance then
            local screenPos, onScreen = Camera:WorldToViewportPoint(aimPartInstance.Position)
            if onScreen then
                local distance = (Vector2.new(screenPos.X, screenPos.Y) - screenCenter).Magnitude
                if distance < fovRadius and distance < closestDistance and isVisible(aimPartInstance) then
                    closestDistance = distance
                    closestTarget = targetData
                end
            end
        end
    end
    return closestTarget
end

function createFOVCircle()
    if FOVCircleFrame then
        FOVCircleFrame:Destroy()
        FOVCircleFrame = nil
    end
    if aimbotRenderConnection then
        aimbotRenderConnection:Disconnect()
        aimbotRenderConnection = nil
    end
    if FOVCircleRotationTask then
        task.cancel(FOVCircleRotationTask)
        FOVCircleRotationTask = nil
    end
    if not ShowFOV then return end

    if not FOVCircleGui then
        FOVCircleGui = Instance.new("ScreenGui")
        FOVCircleGui.Name = "FOVCircleGui"
        FOVCircleGui.IgnoreGuiInset = true
        FOVCircleGui.ResetOnSpawn = false
        FOVCircleGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
        FOVCircleGui.Parent = CoreGui
    end

    local frame = Instance.new("Frame")
    frame.Name = "FOVCircle"
    frame.AnchorPoint = Vector2.new(0.5, 0.5)
    frame.Size = UDim2.new(0, fovRadius * 2, 0, fovRadius * 2)
    frame.BackgroundColor3 = FOVColor
    frame.BackgroundTransparency = 0.85
    frame.BorderSizePixel = 0
    frame.Parent = FOVCircleGui

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(1, 0)
    corner.Parent = frame

    local fillGradient = Instance.new("UIGradient")
    fillGradient.Color = ColorSequence.new(FOVColor)
    fillGradient.Transparency = NumberSequence.new({
        NumberSequenceKeypoint.new(0, 0.4),
        NumberSequenceKeypoint.new(0.5, 0.9),
        NumberSequenceKeypoint.new(1, 0.4),
    })
    fillGradient.Parent = frame

    local stroke = Instance.new("UIStroke")
    stroke.Thickness = FOVThickness
    stroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    stroke.Color = FOVColor
    stroke.Parent = frame

    local strokeGradient = Instance.new("UIGradient")
    strokeGradient.Color = ColorSequence.new(FOVColor)
    strokeGradient.Transparency = NumberSequence.new({
        NumberSequenceKeypoint.new(0, 0),
        NumberSequenceKeypoint.new(0.5, 0.85),
        NumberSequenceKeypoint.new(1, 0),
    })
    strokeGradient.Parent = stroke

    FOVCircleFrame = frame
    FOVCircleStroke = stroke
    FOVCircleFillGradient = fillGradient
    FOVCircleStrokeGradient = strokeGradient

    FOVCircleRotationTask = task.spawn(function()
        while frame.Parent do
            fillGradient.Rotation = (fillGradient.Rotation + 1) % 360
            strokeGradient.Rotation = (strokeGradient.Rotation + 0.5) % 360
            task.wait(0.03)
        end
    end)

    local function UpdatePosition()
        local Camera = workspace.CurrentCamera
        if lockFOVToCenter and Camera then
            local viewportSize = Camera.ViewportSize
            frame.Position = UDim2.new(0, viewportSize.X / 2, 0, viewportSize.Y / 2)
        else
            local mouse = UserInputService:GetMouseLocation()
            frame.Position = UDim2.new(0, mouse.X, 0, mouse.Y)
        end
    end

    UpdatePosition()

    aimbotRenderConnection = RunService.RenderStepped:Connect(function()
        if not FOVCircleFrame or not ShowFOV then return end
        UpdatePosition()
    end)
end

function updateFOVCircle()
    if FOVCircleFrame then
        FOVCircleFrame.Size = UDim2.new(0, fovRadius * 2, 0, fovRadius * 2)
        FOVCircleFrame.Visible = ShowFOV
        FOVCircleFrame.BackgroundColor3 = FOVColor
        if FOVCircleStroke then
            FOVCircleStroke.Color = FOVColor
            FOVCircleStroke.Thickness = FOVThickness
        end
        if FOVCircleFillGradient then
            FOVCircleFillGradient.Color = ColorSequence.new(FOVColor)
        end
        if FOVCircleStrokeGradient then
            FOVCircleStrokeGradient.Color = ColorSequence.new(FOVColor)
        end
    elseif ShowFOV then
        createFOVCircle()
    end
end

function setupCameraLock()
    if cameraInputModule then return true end
    
    local success = false
    
    pcall(function()
        local playerScripts = LocalPlayer:FindFirstChild("PlayerScripts")
        if not playerScripts then return end
        local playerModule = playerScripts:FindFirstChild("PlayerModule")
        if not playerModule then return end
        local cameraModule = playerModule:FindFirstChild("CameraModule")
        if cameraModule then
            local cameraInput = cameraModule:FindFirstChild("CameraInput")
            if cameraInput then
                cameraInputModule = require(cameraInput)
                if cameraInputModule and cameraInputModule.getRotation then
                    local originalGetRotation = cameraInputModule.getRotation
                    cameraInputModule.getRotation = function(disableRotation)
                        if cameraLockEnabled and lockedTarget and lockedTarget.aimPart then
                            local camera = workspace.CurrentCamera
                            if camera then
                                local targetPos = lockedTarget.aimPart.Position
                                local lookVector = (targetPos - camera.CFrame.Position).Unit
                                local targetCFrame = CFrame.new(camera.CFrame.Position, camera.CFrame.Position + lookVector)
                                local smoothFactor = math.clamp(1 - (smoothnessValue / 100), 0.001, 1)
                                camera.CFrame = camera.CFrame:Lerp(targetCFrame, smoothFactor)
                            end
                        end
                        
                        local rotation = originalGetRotation(disableRotation)
                        return rotation
                    end
                    success = true
                end
            end
        end
    end)
    
    return success
end

function startAimbot()
    if aimbotRunning then return end
    createFOVCircle()
    aimbotRunning = true
    aimbotConnection = RunService.RenderStepped:Connect(function()
        if not AimbotEnabled or not aimbotRunning then
            return
        end
        local Camera = workspace.CurrentCamera
        if not Camera then return end
        if not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
            return
        end
        local humanoid = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
        if not humanoid or humanoid.Health <= 0 then
            return
        end
        local closestTarget = getClosestEnemyInFOV()
        if closestTarget and closestTarget.aimPart then
            if not cameraLockEnabled or lockedTarget ~= closestTarget then
                cameraLockEnabled = true
                lockedTarget = closestTarget
                if not cameraInputModule then
                    setupCameraLock()
                end
            end
        else
            cameraLockEnabled = false
            lockedTarget = nil
        end
    end)
end

function stopAimbot()
    aimbotRunning = false
    cameraLockEnabled = false
    lockedTarget = nil
    if aimbotConnection then
        aimbotConnection:Disconnect()
        aimbotConnection = nil
    end
    if FOVCircleFrame then
        FOVCircleFrame:Destroy()
        FOVCircleFrame = nil
    end
    if FOVCircleRotationTask then
        task.cancel(FOVCircleRotationTask)
        FOVCircleRotationTask = nil
    end
    if aimbotRenderConnection then
        aimbotRenderConnection:Disconnect()
        aimbotRenderConnection = nil
    end
    if cameraLockConnection then
        cameraLockConnection:Disconnect()
        cameraLockConnection = nil
    end
end

secCombatAuto1 = Tabs.Combat:AddSection("Aimbot", "solar/widget-2-bold")

AimbotToggle = secCombatAuto1:AddToggle("AimbotToggle", {
    Title = "Aimbot",
    Default = false,
    Callback = function(DConfiguration)
        AimbotEnabled = DConfiguration
        if DConfiguration then
            startAimbot()
        else
            stopAimbot()
        end
    end
})

AimPartDropdown = secCombatAuto1:AddDropdown("AimPartDropdown", {
    Search = false,
    Title = "Aim Part",
    Values = { "Head", "Body", "Legs" },
    Default = "Head",
    Callback = function(value)
        aimPart = value
    end
})

roleValues = getRoleListValues()

TargetTypeDropdown = secCombatAuto1:AddDropdown("TargetTypeDropdown", {
    DropdownOutsideWindow = true,
    Title = "Target Roles",
    Values = roleValues,
    Default = {},
    Multi = true,
    AllowNone = true,
    Callback = function(values)
        targetTypes = values
    end
})

AimLockTypeDropdown = secCombatAuto1:AddDropdown("AimLockTypeDropdown", {
    Search = false,
    Title = "Aim Lock Type",
    Values = { "Realistic", "Stimulate" },
    Default = "Realistic",
    Callback = function(value)
        aimLockType = value
    end
})

secCombatAuto1:AddSpace({ Height = 20 })
SmoothnessSlider = secCombatAuto1:AddSlider("SmoothnessSlider", {
    Title = "Smoothness",
    Rounding = 0.01,
    Min = 0.01,
    Max = 100,
    Default = 10,
    Callback = function(value)
        smoothnessValue = tonumber(value) or 10
    end
})

MaxDistanceInput = secCombatAuto1:AddInput("CombatInput29", {
    Title = "Max Distance",
    Default = "99999999999",
    Placeholder = "Enter max distance",
    Callback = function(value)
        local numValue = tonumber(value)
        if numValue then
            maxDistance = numValue
        end
    end
})

WallCheckToggle = secCombatAuto1:AddToggle("WallCheckToggle", {
    Title = "Wall Check",
    Default = false,
    Callback = function(DConfiguration)
        wallCheckEnabled = DConfiguration
    end
})

Tabs.Combat:AddSpace({ Height = 20 })
secCombatAuto2 = Tabs.Combat:AddSection("FOV Settings", "solar/widget-2-bold")

ShowFOVToggle = secCombatAuto2:AddToggle("ShowFOVToggle", {
    Title = "Show FOV Circle",
    Default = false,
    Callback = function(DConfiguration)
        ShowFOV = DConfiguration
        updateFOVCircle()
    end
})

LockFOVToggle = secCombatAuto2:AddToggle("LockFOVToggle", {
    Title = "Lock FOV On Middle Screen",
    Default = true,
    Callback = function(DConfiguration)
        lockFOVToCenter = DConfiguration
        updateFOVCircle()
    end
})

FOVRadiusSlider = secCombatAuto2:AddSlider("FOVRadiusSlider", {
    Title = "FOV Radius",
    Min = 10,
    Max = 500,
    Default = 100,
    Rounding = 5,
    Callback = function(value)
        fovRadius = tonumber(value) or 100
        updateFOVCircle()
    end
})

FOVColorPicker = secCombatAuto2:AddColorpicker("FOVColorPicker", {
    Title = "FOV Color",
    Default = Color3.fromRGB(0, 120, 255),
    Callback = function(color)
        FOVColor = color
        updateFOVCircle()
    end
})

secCombatAuto2:AddSpace({ Height = 20 })
FOVThicknessSlider = secCombatAuto2:AddSlider("FOVThicknessSlider", {
    Title = "FOV Thickness",
    Min = 1,
    Max = 10,
    Default = 2,
    Rounding = 1,
    Callback = function(value)
        FOVThickness = tonumber(value) or 2
        updateFOVCircle()
    end
})
Tabs.Combat:AddSpace({ Height = 20 })
secCombatAuto3 = Tabs.Combat:AddSection("Gun Combat", "solar/widget-2-bold")
secCombatAuto3:AddDivider()

autoShootEnabled = false
shootOffset = 0
pingMultiplier = 0
wallCheckEnabled = false
predictionEnabled = true
magicBulletSideOffset = 5

function IsMurdererBehindWall(murdererCharacter)
    if not wallCheckEnabled then return false end

    local camera = workspace.CurrentCamera
    if not camera then return false end

    local character = LocalPlayer.Character
    if not character then return true end

    local cameraPos = camera.CFrame.Position

    local targetPart = murdererCharacter:FindFirstChild("HumanoidRootPart") or 
                       murdererCharacter:FindFirstChild("Head") or
                       murdererCharacter:FindFirstChild("UpperTorso") or
                       murdererCharacter:FindFirstChild("Torso")

    if not targetPart then return true end

    local raycastParams = RaycastParams.new()
    raycastParams.FilterType = Enum.RaycastFilterType.Blacklist
    raycastParams.FilterDescendantsInstances = {character, murdererCharacter, camera}

    local rayResult = workspace:Raycast(cameraPos, (targetPart.Position - cameraPos), raycastParams)

    if rayResult then
        local hit = rayResult.Instance
        if hit and hit:IsA("BasePart") then
            local isWindow = hit.Name:lower():find("window") or 
                             hit.Name:lower():find("glass") or
                             hit.Material == Enum.Material.Glass

            if not isWindow and not hit:IsDescendantOf(character) and not hit:IsDescendantOf(murdererCharacter) then
                return true
            end
        end
    end

    return false
end

MagicBulletEnabled = false

function ShootMurderer()
    if not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild("Humanoid") or LocalPlayer.Character.Humanoid.Health <= 0 then
        return false
    end
    local murderer = GetMurderer()
    if not murderer or not murderer.Character or not murderer.Character:FindFirstChild("Humanoid") or murderer.Character.Humanoid.Health <= 0 then
        return false
    end
    if not MagicBulletEnabled then
        if IsMurdererBehindWall(murderer.Character) then
            return false
        end
    end
    local targetPart = murderer.Character:FindFirstChild("HumanoidRootPart") or 
                       murderer.Character:FindFirstChild("Head") or
                       murderer.Character:FindFirstChild("UpperTorso") or
                       murderer.Character:FindFirstChild("Torso")
    if not targetPart then
        return false
    end
    local targetPos = targetPart.Position
    local targetVelocity = Vector3.new(0, 0, 0)
    local murdererHumanoid = murderer.Character:FindFirstChildOfClass("Humanoid")
    if murdererHumanoid then
        local rootPart = murderer.Character:FindFirstChild("HumanoidRootPart")
        if rootPart and rootPart:FindFirstChild("AssemblyLinearVelocity") then
            targetVelocity = rootPart.AssemblyLinearVelocity
        elseif murdererHumanoid.MoveDirection.Magnitude > 0 then
            local walkSpeed = murdererHumanoid.WalkSpeed
            targetVelocity = murdererHumanoid.MoveDirection * walkSpeed
        end
    end
    
    if predictionEnabled then
        local ping = game:GetService("Stats").Network.ServerStatsItem["Data Ping"]:GetValue() / 1000
        local predictionTime = ping + 0.05 
        if targetVelocity.Magnitude > 0 then
            targetPos = targetPos + (targetVelocity * predictionTime)
        end
        if shootOffset ~= 0 and murdererHumanoid then
            local moveDirection = murdererHumanoid.MoveDirection
            if moveDirection.Magnitude > 0 then
                local offsetDirection = moveDirection.Unit
                local finalOffset = shootOffset * (pingMultiplier or 1) * predictionTime * 10
                targetPos = targetPos + (offsetDirection * finalOffset)
            end
        end
    end
    
    local gun = LocalPlayer.Character:FindFirstChild("Gun") or LocalPlayer.Backpack:FindFirstChild("Gun")
    if not gun then
        return false
    end
    local shootEvent = gun:FindFirstChild("Shoot")
    if not shootEvent then
        return false
    end
    
    local character = LocalPlayer.Character
    local isGunEquipped = character:FindFirstChild("Gun") ~= nil
    local shootOrigin
    
    if isGunEquipped then
        local handle = gun:FindFirstChild("Handle") or gun:FindFirstChildWhichIsA("Part")
        if handle then
            shootOrigin = handle.Position
        else
            local rightHand = character:FindFirstChild("RightHand")
            if rightHand then
                shootOrigin = rightHand.Position
            else
                shootOrigin = character:FindFirstChild("HumanoidRootPart").Position
            end
        end
    else
        local rightHand = character:FindFirstChild("RightHand")
        if rightHand then
            shootOrigin = rightHand.Position
        else
            shootOrigin = character:FindFirstChild("HumanoidRootPart").Position
        end
    end
    
    local lookAtCFrame
    local targetCFrame = CFrame.new(targetPos)
    
    if MagicBulletEnabled then
        local rightDirection = CFrame.new(shootOrigin, targetPos).RightVector
        local sideOffset = magicBulletSideOffset
        local sidePos = targetPos + (rightDirection * sideOffset)
        local lookDirection = (targetPos - sidePos).Unit
        lookAtCFrame = CFrame.new(sidePos, sidePos + lookDirection)
    else
        local gunCFrame = CFrame.new(shootOrigin, targetPos)
        lookAtCFrame = gunCFrame
    end
    
    shootEvent:FireServer(lookAtCFrame, targetCFrame)
    return true
end

function startAutoShoot()
    while autoShootEnabled do
        ShootMurderer()
        task.wait(0.1)
    end
end

AutoShootToggle = secCombatAuto3:AddToggle("AutoShoot", {
    Title = "Auto Shoot Murderer",
    Default = false,
    Callback = function(DConfiguration)
        autoShootEnabled = DConfiguration
        if DConfiguration then
            task.spawn(startAutoShoot)
        end
        FloatingButtonModule.SetActive(AutoShootFloatButton, DConfiguration, "Auto Shoot Murder")
    end
})

secCombatAuto3:AddToggle("AutoShootBtnVisibleToggle", {
    Title = "Show Auto Shoot Murder Button",
    Default = false,
    Callback = function(DConfiguration)
        FloatingButtonModule.SetVisible(AutoShootFrame, DConfiguration)
    end,
})

FBModule:AddSizeInputs(secCombatAuto3, "AutoShootBtn", "Auto Shoot Murder")

AutoShootFrame, AutoShootFloatButton = FBModule:Create("AutoShootBtn", "Auto Shoot Murder", true, function(Btn)
    autoShootEnabled = not autoShootEnabled
    if autoShootEnabled then
        task.spawn(startAutoShoot)
    end
    FloatingButtonModule.SetActive(Btn, autoShootEnabled, "Auto Shoot Murder")
    if AutoShootToggle then
        AutoShootToggle:SetValue(autoShootEnabled)
    end
end)

secCombatAuto3:AddToggle("MagicBullet", {
    Title = "Magic Bullet",
    Description = "Allow Shoot through walls (70% work)",
    Default = false,
    Callback = function(DConfiguration)
        MagicBulletEnabled = DConfiguration
        if DConfiguration then
            FloatingButtonModule.SetText(SheriffButton, "MAGIC BULLET SHOOT")
            if manualShootMurd then
                manualShootMurd:SetTitle("MAGIC BULLET BUTTON")
            end
            if ShootKeybind then
                ShootKeybind:SetTitle("MAGIC BULLET KEY")
            end
        else
            FloatingButtonModule.SetText(SheriffButton, "Shoot Murderer")
            if manualShootMurd then
                manualShootMurd:SetTitle("Shoot Murderer")
            end
            if ShootKeybind then
                ShootKeybind:SetTitle("Shoot Murderer Keybind")
            end
        end
    end
})

secCombatAuto3:AddToggle("ShootWallCheck", {
    Title = "Wall Check (Prevent shooting through walls)",
    Type = "Checkbox",
    Default = false,
    Callback = function(DConfiguration)
        wallCheckEnabled = DConfiguration
    end
})

secCombatAuto3:AddSpace({ Height = 20 })
secCombatAuto3:AddToggle("PredictionToggle", {
    Title = "Enable Prediction",
    Description = "Predict target movement for better accuracy",
    Default = true,
    Callback = function(DConfiguration)
        predictionEnabled = DConfiguration
    end
})

secCombatAuto3:AddInput("MagicBulletSideOffset", {
    Title = "Magic Bullet SideOffset",
    Placeholder = "5",
    Default = "5",
    Callback = function(text)
        local num = tonumber(text)
        if num then
            magicBulletSideOffset = num
        end
    end
})

secCombatAuto3:AddInput("ShootPositionOffset", {
    Title = "Shoot Position Offset",
    Placeholder = "0",
    Default = "0",
    Callback = function(text)
        shootOffset = tonumber(text) or 0
    end
})

secCombatAuto3:AddInput("Offset-to-PingMultiplier", {
    Title = "Offset-to-Ping Multiplier",
    Placeholder = "0",
    Default = "0",
    Callback = function(text)
        pingMultiplier = tonumber(text) or 1
    end
})

manualShootMurd = secCombatAuto3:AddButton({
    Title = "Shoot Murderer",
    Callback = function()
        ShootMurderer()
    end
})

secCombatAuto3:AddSpace({ Height = 20 })
ShootKeybind = secCombatAuto3:AddKeybind("CombatKeybind46", {
    Title = "Shoot Murderer Keybind",
    Default = "E",
    Callback = function()
    ShootMurderer()
    end
})

secCombatAuto3:AddToggle("SheriffBtnVisibleToggle", {
    Title = "Show Shoot Murder Button",
    Default = false,
    Callback = function(DConfiguration)
        SheriffBtnVisible = DConfiguration
        FloatingButtonModule.SetVisible(SheriffFrame, DConfiguration)
    end,
})

FBModule:AddSizeInputs(secCombatAuto3, "SheriffBtn", "Shoot Murder")

SheriffFrame, SheriffButton = FBModule:Create("SheriffBtn", "Shoot Murder", false, function() ShootMurderer() end)
GunSystem = {
    AutoGrabEnabled = false,
    NotifyGun = false,
    GunDropCheckInterval = 1,
    ActiveGunDrops = {},
    Mode = "Grab only"
}

notifiedGunPickups = {}
notifiedGunSpawns = {}

function GunTP()
    local gunDrop = nil

    for _, obj in pairs(workspace:GetDescendants()) do
        if obj.Name == "GunDrop" and obj:IsA("BasePart") then
            gunDrop = obj
            break
        end
    end

    if gunDrop and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(gunDrop.Position + Vector3.new(0, 3, 0))
    end
end

function ScanForGunDrops()
    GunSystem.ActiveGunDrops = {}
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj.Name == "GunDrop" and obj:IsA("BasePart") then
            table.insert(GunSystem.ActiveGunDrops, obj)
        end
    end
end

function safeTeleport(cframe)
    pcall(function()
        if Character and HumanoidRootPart then
            HumanoidRootPart.CFrame = cframe
        end
    end)
end

function hasKnife()
    return LocalPlayer.Backpack:FindFirstChild("Knife") or (LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Knife"))
end

function collectAllGunDrops()
    if hasKnife() then
        Fluent:Notify({Title = "Gun System", Content = "You already have a knife!", Icon = "x-circle", Duration = 3})
        return
    end

    local currentPosition = HumanoidRootPart.Position
    ScanForGunDrops()

    if #GunSystem.ActiveGunDrops == 0 then
        Fluent:Notify({Title = "Gun System", Content = "No guns available on the map", Icon = "x-circle", Duration = 3})
        return
    end

    for _, gunDrop in ipairs(GunSystem.ActiveGunDrops) do
        if gunDrop and gunDrop.Parent then
            safeTeleport(gunDrop.CFrame + Vector3.new(0, 3, 0))
            task.wait(0.05)
            safeTeleport(CFrame.new(currentPosition))
            task.wait(0.05)
        end
    end

    Fluent:Notify({Title = "Gun System", Content = "Successfully collected all guns!", Icon = "check-circle", Duration = 3})
end

function ManualGrab()
    if IsPlayerDead() then
        Fluent:Notify({Title = "Gun System", Content = "You are dead! Cannot grab gun.", Icon = "skull", Duration = 3})
        return false
    end

    if hasKnife() then
        Fluent:Notify({Title = "Gun System", Content = "You already have a knife!", Icon = "x-circle", Duration = 3})
        return false
    end

    if GunSystem.Mode == "Grab only" then
        collectAllGunDrops()
        return true
    elseif GunSystem.Mode == "Grab & shoot murderer" then
        local gunDrop = nil
        for _, obj in pairs(workspace:GetDescendants()) do
            if obj.Name == "GunDrop" and obj:IsA("BasePart") then
                gunDrop = obj
                break
            end
        end

        if not gunDrop then
            Fluent:Notify({Title = "Gun System", Content = "No guns available on the map", Icon = "x-circle", Duration = 3})
            return false
        end

        local nearestGun = nil
        local minDistance = math.huge
        local character = LocalPlayer.Character
        local humanoidRootPart = character and character:FindFirstChild("HumanoidRootPart")

        if humanoidRootPart then
            for _, obj in pairs(workspace:GetDescendants()) do
                if obj.Name == "GunDrop" and obj:IsA("BasePart") then
                    local distance = (humanoidRootPart.Position - obj.Position).Magnitude
                    if distance < minDistance then
                        nearestGun = obj
                        minDistance = distance
                    end
                end
            end
        end

        if nearestGun and LocalPlayer.Character then
            if IsPlayerDead() then
                Fluent:Notify({Title = "Gun System", Content = "You are dead! Cannot grab gun.", Icon = "skull", Duration = 3})
                return false
            end

            local humanoidRootPart = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
            if humanoidRootPart then
                humanoidRootPart.CFrame = nearestGun.CFrame
                task.wait(0.3)

                if IsPlayerDead() then
                    Fluent:Notify({Title = "Gun System", Content = "You died while trying to grab the gun!", Icon = "skull", Duration = 3})
                    return false
                end

                local prompt = nearestGun:FindFirstChildOfClass("ProximityPrompt")
                if prompt then
                    fireproximityprompt(prompt)
                    Fluent:Notify({Title = "Gun System", Content = "Successfully grabbed the gun!", Icon = "check-circle", Duration = 3})
                    task.wait(0.5)
                    ShootMurderer()
                    return true
                end
            end
        end
        return false
    end
end

function ImprovedGrabOnly()
    local isTeleporting = false
    local teleportDelay = 0.5

    function teleportLoop()
        if isTeleporting then return end

        if IsPlayerDead() then
            return
        end

        if hasKnife() then return end

        local character = LocalPlayer.Character
        if not character then return end

        local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
        if not humanoidRootPart then return end

        isTeleporting = true

        local currentPosition = humanoidRootPart.Position
        ScanForGunDrops()

        if #GunSystem.ActiveGunDrops == 0 then
            isTeleporting = false
            return
        end

        for _, gunDrop in ipairs(GunSystem.ActiveGunDrops) do
            if IsPlayerDead() then
                break
            end

            if gunDrop and gunDrop.Parent then
                safeTeleport(gunDrop.CFrame + Vector3.new(0, 3, 0))
                task.wait(0.05)
                safeTeleport(CFrame.new(currentPosition))
                task.wait(0.05)
            end
        end

        isTeleporting = false
    end

    while GunSystem.AutoGrabEnabled and GunSystem.Mode == "Grab only" do
        if not IsPlayerDead() and not hasKnife() then
            teleportLoop()
        end
        task.wait(teleportDelay)
    end
end

function AutoGrabGun()
    while GunSystem.AutoGrabEnabled do
        if IsPlayerDead() then
            task.wait(GunSystem.GunDropCheckInterval)
        elseif hasKnife() then
            task.wait(GunSystem.GunDropCheckInterval)
        else
            if GunSystem.Mode == "Grab only" then
                ImprovedGrabOnly()
            elseif GunSystem.Mode == "Grab & shoot murderer" then
                ScanForGunDrops()
                if #GunSystem.ActiveGunDrops > 0 then
                    if not IsPlayerDead() then
                        local character = LocalPlayer.Character
                        if character then
                            local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
                            if humanoidRootPart then
                                local nearestGun = nil
                                local minDistance = math.huge
                                for _, gunDrop in ipairs(GunSystem.ActiveGunDrops) do
                                    local distance = (humanoidRootPart.Position - gunDrop.Position).Magnitude
                                    if distance < minDistance then
                                        nearestGun = gunDrop
                                        minDistance = distance
                                    end
                                end
                                if nearestGun then
                                    humanoidRootPart.CFrame = nearestGun.CFrame
                                    task.wait(0.3)

                                    if not IsPlayerDead() then
                                        local prompt = nearestGun:FindFirstChildOfClass("ProximityPrompt")
                                        if prompt then
                                            fireproximityprompt(prompt)
                                            task.wait(0.5)
                                            ShootMurderer()
                                            task.wait(1)
                                        end
                                    end
                                end
                            end
                        end
                    end
                end
                task.wait(GunSystem.GunDropCheckInterval)
            end
        end
    end
end

function monitorGunEvents()
    for _, otherPlayer in ipairs(Players:GetPlayers()) do
        if otherPlayer ~= LocalPlayer then
            otherPlayer.CharacterAdded:Connect(function(character)
                character.ChildAdded:Connect(function(child)
                    if child.Name == "Gun" and GunSystem.NotifyGun then
                        if not notifiedGunPickups[otherPlayer.Name] then
                            Fluent:Notify({
                                Title = "Gun System", 
                                Content = otherPlayer.Name .. " took the gun!", 
                                Icon = "alert-circle", 
                                Duration = 5
                            })
                            notifiedGunPickups[otherPlayer.Name] = true
                        end
                    end
                end)
            end)

            if otherPlayer.Character then
                otherPlayer.Character.ChildAdded:Connect(function(child)
                    if child.Name == "Gun" and GunSystem.NotifyGun then
                        if not notifiedGunPickups[otherPlayer.Name] then
                            Fluent:Notify({
                                Title = "Gun System", 
                                Content = otherPlayer.Name .. " took the gun!", 
                                Icon = "alert-circle", 
                                Duration = 5
                            })
                            notifiedGunPickups[otherPlayer.Name] = true
                        end
                    end
                end)
            end
        end
    end

    workspace.DescendantAdded:Connect(function(child)
        if child.Name == "GunDrop" and child:IsA("BasePart") and GunSystem.NotifyGun then
            if not notifiedGunSpawns[child] then
                Fluent:Notify({
                    Title = "Gun System", 
                    Content = "A gun has spawned!", 
                    Icon = "target", 
                    Duration = 5
                })
                notifiedGunSpawns[child] = true
            end
        end
    end)
end

function resetGunNotifications()
    workspace.DescendantAdded:Connect(function(child)
        if child.Name == "GunDrop" and child:IsA("BasePart") then
            for playerName, _ in pairs(notifiedGunPickups) do
                notifiedGunPickups[playerName] = nil
            end
            notifiedGunSpawns[child] = nil
        end
    end)
end

LocalPlayer.CharacterAdded:Connect(function(newChar)
    setupCharacter(newChar)
end)

secCombatAuto3:AddToggle("TPStealGun", {
    Title = "Auto Grab Gun",
    Description = "Auto Steal Gun by teleport",
    Default = false,
    Callback = function(DConfiguration)
        GunSystem.AutoGrabEnabled = DConfiguration
        if DConfiguration then
            coroutine.wrap(AutoGrabGun)()
        end
    end
})

GunAuraEnabled = false
GunAuraConnection = nil

function touch(a, b)
    firetouchinterest(a, b, 0)
    firetouchinterest(a, b, 1)
end

function BringGun()
    local character = LocalPlayer.Character
    local rootPart = character and character:FindFirstChild("HumanoidRootPart")
    local gunDrop = workspace:FindFirstChild("GunDrop", true)

    if rootPart and gunDrop then
        touch(rootPart, gunDrop)
    end
end

secCombatAuto3:AddToggle("TouchInstantFireGun", {
    Title = "Gun Aura",
    Description = "Auto Steal Gun without teleport",
    Default = false,
    Callback = function(DConfiguration)
        GunAuraEnabled = DConfiguration
        if DConfiguration then
            GunAuraConnection = RunService.Heartbeat:Connect(function()
                if GunAuraEnabled then
                    BringGun()
                end
            end)
        else
            if GunAuraConnection then
                GunAuraConnection:Disconnect()
                GunAuraConnection = nil
            end
        end
    end
})

secCombatAuto3:AddButton({
    Title = "Manual Grab Gun",
    Callback = function()
        ManualGrab()
    end
})

secCombatAuto3:AddSpace({ Height = 20 })
secCombatAuto3:AddDropdown("CombatDropdown51", {
    Search = false,
    Title = "Auto Grab Mode",
    Values = {"Grab only", "Grab & shoot murderer"},
    Default = "Grab only",
    Callback = function(value)
        GunSystem.Mode = value
    end
})

secCombatAuto3:AddToggle("NotifyGunDrop", {
    Title = "Notify Gun",
    Default = false,
    Callback = function(DConfiguration)
        GunSystem.NotifyGun = DConfiguration
    end
})

task.spawn(function()
    if not LocalPlayer.Character then
        LocalPlayer.CharacterAdded:Wait()
    end
    ScanForGunDrops()
    if GunSystem.AutoGrabEnabled then
        coroutine.wrap(AutoGrabGun)()
    end
    monitorGunEvents()
    resetGunNotifications()
end)

Tabs.Combat:AddSpace({ Height = 20 })
secCombatAuto4 = Tabs.Combat:AddSection("Knife Combat", "solar/widget-2-bold")
secCombatAuto4:AddDivider()

KnifeCombat = {
    killMode = "Kill Aura",
    killAuraRadius = 10,
    autoKillEnabled = false,
    showAuraCircle = false,
    autoEquipKnife = false,
    killConnection = nil,
    auraConnection = nil,
    equipConnection = nil,
    anchoredPlayers = {},
    auraCircle = nil,
    autoThrowKnife = false,
    throwKnifeConnection = nil,
    wallCheckType = {"None"},
    StabReach = {
        Enabled = false,
        Radius = 10,
        Connection = nil,
        KnifeAddedConnection = nil
    },
    HitboxConfig = {
        Enabled = false,
        Radius = 10,
        MultipleTargets = false
    },
    hitboxHeartbeatConnection = nil,
    hitboxDescendantConnection = nil,
    lastHitboxCheck = 0,
    hitboxCheckCooldown = 0.1
}

function KnifeCombat.getPlayerRole(plr)
    return GetPlayerRole(plr.Name)
end

function KnifeCombat.isTargetVisible(targetPart)
    if not table.find(KnifeCombat.wallCheckType, "Manual Throw Knife") and 
       not table.find(KnifeCombat.wallCheckType, "Auto Throw Knife") then
        return true
    end

    local localCharacter = LocalPlayer.Character
    if not localCharacter then return false end

    local localHead = localCharacter:FindFirstChild("Head")
    local targetHead = targetPart.Parent:FindFirstChild("Head")

    if not localHead or not targetHead then return false end

    local raycastParams = RaycastParams.new()
    raycastParams.FilterType = Enum.RaycastFilterType.Blacklist
    raycastParams.FilterDescendantsInstances = {localCharacter, targetPart.Parent}

    local direction = (targetHead.Position - localHead.Position)
    local raycastResult = workspace:Raycast(localHead.Position, direction, raycastParams)

    return raycastResult == nil
end

function KnifeCombat.getBestTarget()
    local localCharacter = LocalPlayer.Character
    if not localCharacter then return nil end

    local localRoot = localCharacter:FindFirstChild("HumanoidRootPart")
    if not localRoot then return nil end

    local bestTarget = nil
    local bestDistance = math.huge
    local sheriffHeroTarget = nil
    local sheriffHeroDistance = math.huge

    for _, targetPlayer in ipairs(Players:GetPlayers()) do
        if targetPlayer ~= LocalPlayer and targetPlayer.Character then
            local targetRoot = targetPlayer.Character:FindFirstChild("HumanoidRootPart")
            local targetHumanoid = targetPlayer.Character:FindFirstChildOfClass("Humanoid")

            if targetRoot and targetHumanoid and targetHumanoid.Health > 0 then
                local distance = (targetRoot.Position - localRoot.Position).Magnitude
                local role = KnifeCombat.getPlayerRole(targetPlayer)

                if role == "Sheriff" or role == "Hero" then
                    if distance < sheriffHeroDistance then
                        sheriffHeroTarget = targetPlayer
                        sheriffHeroDistance = distance
                    end
                elseif role == "Innocent" or role == nil then
                    if distance < bestDistance then
                        bestTarget = targetPlayer
                        bestDistance = distance
                    end
                end
            end
        end
    end

    local finalTarget = nil
    if sheriffHeroTarget and sheriffHeroDistance < 100 then
        finalTarget = sheriffHeroTarget
    else
        finalTarget = bestTarget
    end

    if finalTarget and finalTarget.Character then
        local targetRoot = finalTarget.Character:FindFirstChild("HumanoidRootPart")
        if targetRoot then
            local needsManualWallCheck = table.find(KnifeCombat.wallCheckType, "Manual Throw Knife")
            local needsAutoWallCheck = table.find(KnifeCombat.wallCheckType, "Auto Throw Knife")

            if (needsManualWallCheck or needsAutoWallCheck) and not KnifeCombat.isTargetVisible(targetRoot) then
                return nil
            end
        end
    end

    return finalTarget
end

function KnifeCombat.GetKnife()
    local char = LocalPlayer.Character
    if not char then return nil end

    local knife = char:FindFirstChild("Knife")
    if knife then return knife end

    local backpack = LocalPlayer:FindFirstChild("Backpack")
    if backpack then
        knife = backpack:FindFirstChild("Knife")
    end

    return knife
end

function KnifeCombat.GetMurderer()
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr.Backpack:FindFirstChild("Knife") or (plr.Character and plr.Character:FindFirstChild("Knife")) then
            return plr
        end
    end
    return nil
end

function KnifeCombat.getKnifeRemotes()
    local knife = KnifeCombat.GetKnife()
    local events = nil
    local remotes = {}

    if not knife then
        knife = workspace:FindFirstChild("Knife") or ReplicatedStorage:FindFirstChild("Knife")
    end

    if not knife then
        local knives = ReplicatedStorage:FindFirstChild("Knives")
        if knives then
            knife = knives:FindFirstChild("Knife")
        end
    end

    if knife then
        events = knife:FindFirstChild("Events")
    end

    if not events then
        local function findKnifeRemotes(obj)
            for _, child in pairs(obj:GetChildren()) do
                if child:IsA("RemoteEvent") and (child.Name:find("Knife") or child.Name:find("Stab") or child.Name:find("Throw")) then
                    table.insert(remotes, child)
                end
                findKnifeRemotes(child)
            end
        end
        findKnifeRemotes(ReplicatedStorage)
        findKnifeRemotes(workspace)
    else
        for _, child in pairs(events:GetChildren()) do
            if child:IsA("RemoteEvent") then
                table.insert(remotes, child)
            end
        end
    end

    return remotes
end

function KnifeCombat.attackPlayer(targetChar)
    if not targetChar then return end
    local humanoid = targetChar:FindFirstChild("Humanoid")
    if not humanoid or humanoid.Health <= 0 then return end

    local remotes = KnifeCombat.getKnifeRemotes()
    if #remotes == 0 then return end

    for _, remote in pairs(remotes) do
        pcall(function()
            if remote.Name == "KnifeStabbed" then
                remote:FireServer()
            elseif remote.Name == "HandleTouched" then
                local part = targetChar:FindFirstChild("Head") or 
                             targetChar:FindFirstChild("HumanoidRootPart") or
                             targetChar:FindFirstChild("Torso")
                if part then
                    remote:FireServer(part)
                end
            elseif remote.Name == "KnifeThrown" then
                local targetPos = targetChar.PrimaryPart and targetChar.PrimaryPart.Position or 
                                  (targetChar:FindFirstChild("HumanoidRootPart") and targetChar.HumanoidRootPart.Position)
                if targetPos then
                    local fakeHandle = Instance.new("Part")
                    fakeHandle.Name = "Handle"
                    fakeHandle.CFrame = CFrame.new(0, 9999, 0)
                    fakeHandle.Parent = workspace
                    fakeHandle.Transparency = 1
                    game:GetService("Debris"):AddItem(fakeHandle, 1)
                    remote:FireServer(fakeHandle.CFrame, targetPos)
                end
            else
                remote:FireServer()
                remote:FireServer(targetChar)
                remote:FireServer(humanoid)
            end
        end)
    end
end

function KnifeCombat.stabReachAttack()
    local localRoot = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not localRoot then return end

    for _, targetPlayer in ipairs(Players:GetPlayers()) do
        if targetPlayer.Character and targetPlayer ~= LocalPlayer then
            local targetRoot = targetPlayer.Character:FindFirstChild("HumanoidRootPart") or targetPlayer.Character:FindFirstChild("Torso")
            if targetRoot then
                local distance = (targetRoot.Position - localRoot.Position).Magnitude
                if distance <= KnifeCombat.StabReach.Radius then
                    KnifeCombat.attackPlayer(targetPlayer.Character)
                end
            end
        end
    end
end

function KnifeCombat.connectKnifeActivated(knife)
    if not knife then return end

    if KnifeCombat.StabReach.Connection then
        KnifeCombat.StabReach.Connection:Disconnect()
    end

    KnifeCombat.StabReach.Connection = knife.Activated:Connect(function()
        if KnifeCombat.StabReach.Enabled and KnifeCombat.GetMurderer() == LocalPlayer then
            KnifeCombat.stabReachAttack()
        end
    end)
end

function KnifeCombat.startStabReach()
    KnifeCombat.stopStabReach()

    local knife = KnifeCombat.GetKnife()
    if knife then
        KnifeCombat.connectKnifeActivated(knife)
    end

    if KnifeCombat.StabReach.KnifeAddedConnection then
        KnifeCombat.StabReach.KnifeAddedConnection:Disconnect()
    end

    KnifeCombat.StabReach.KnifeAddedConnection = LocalPlayer.CharacterAdded:Connect(function(char)
        task.wait(0.5)
        char.ChildAdded:Connect(function(child)
            if child.Name == "Knife" and KnifeCombat.StabReach.Enabled then
                task.wait(0.1)
                KnifeCombat.connectKnifeActivated(child)
            end
        end)

        if LocalPlayer.Backpack then
            LocalPlayer.Backpack.ChildAdded:Connect(function(child)
                if child.Name == "Knife" and KnifeCombat.StabReach.Enabled then
                    task.wait(0.1)
                    KnifeCombat.connectKnifeActivated(child)
                end
            end)
        end
    end)

    if LocalPlayer.Character then
        LocalPlayer.Character.ChildAdded:Connect(function(child)
            if child.Name == "Knife" and KnifeCombat.StabReach.Enabled then
                task.wait(0.1)
                KnifeCombat.connectKnifeActivated(child)
            end
        end)
    end

    if LocalPlayer.Backpack then
        LocalPlayer.Backpack.ChildAdded:Connect(function(child)
            if child.Name == "Knife" and KnifeCombat.StabReach.Enabled then
                task.wait(0.1)
                KnifeCombat.connectKnifeActivated(child)
            end
        end)
    end
end

function KnifeCombat.stopStabReach()
    if KnifeCombat.StabReach.Connection then
        KnifeCombat.StabReach.Connection:Disconnect()
        KnifeCombat.StabReach.Connection = nil
    end
    if KnifeCombat.StabReach.KnifeAddedConnection then
        KnifeCombat.StabReach.KnifeAddedConnection:Disconnect()
        KnifeCombat.StabReach.KnifeAddedConnection = nil
    end
end

function KnifeCombat.throwKnife()
    if not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChildOfClass("Humanoid") or 
       LocalPlayer.Character:FindFirstChildOfClass("Humanoid").Health <= 0 then
        return false
    end

    local knife = LocalPlayer.Character:FindFirstChild("Knife")
    if not knife then
        local backpack = LocalPlayer:FindFirstChild("Backpack")
        if backpack then
            knife = backpack:FindFirstChild("Knife")
            if knife then
                local humanoid = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
                if humanoid then
                    humanoid:EquipTool(knife)
                end
                task.wait(0.2)
            end
        end
    end

    if not knife then return false end

    local eventsFolder = knife:FindFirstChild("Events")
    if not eventsFolder then return false end

    local throwEvent = eventsFolder:FindFirstChild("KnifeThrown")
    if not throwEvent then return false end

    local targetPlayer = KnifeCombat.getBestTarget()
    if not targetPlayer or not targetPlayer.Character then return false end

    local targetRoot = targetPlayer.Character:FindFirstChild("HumanoidRootPart") or 
                       targetPlayer.Character:FindFirstChild("Head") or
                       targetPlayer.Character:FindFirstChild("UpperTorso") or
                       targetPlayer.Character:FindFirstChild("Torso")
    if not targetRoot then return false end

    local localRoot = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not localRoot then return false end

    local targetPos = targetRoot.Position
    local distance = (targetPos - localRoot.Position).Magnitude
    local pitchAngle = math.clamp(distance / 50, 10, 45)

    local startCFrame = localRoot.CFrame * CFrame.new(0, 1.5, -2) * CFrame.Angles(math.rad(-pitchAngle), 0, 0)
    local targetCFrame = CFrame.new(targetPos + Vector3.new(0, 1.5, 0))

    throwEvent:FireServer(startCFrame, targetCFrame)
    return true
end

function KnifeCombat.killAura()
    local localRoot = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not localRoot then return end

    for _, targetPlayer in ipairs(Players:GetPlayers()) do
        if targetPlayer.Character and targetPlayer ~= LocalPlayer then
            local targetRoot = targetPlayer.Character:FindFirstChild("HumanoidRootPart") or targetPlayer.Character:FindFirstChild("Torso")
            if targetRoot then
                local distance = (targetRoot.Position - localRoot.Position).Magnitude
                if distance <= KnifeCombat.killAuraRadius then
                    KnifeCombat.attackPlayer(targetPlayer.Character)
                end
            end
        end
    end
end

function KnifeCombat.killNearby()
    local localRoot = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not localRoot then return end

    for _, targetPlayer in ipairs(Players:GetPlayers()) do
        if targetPlayer.Character and targetPlayer ~= LocalPlayer then
            local targetRoot = targetPlayer.Character:FindFirstChild("HumanoidRootPart") or targetPlayer.Character:FindFirstChild("Torso")
            if targetRoot then
                local distance = (targetRoot.Position - localRoot.Position).Magnitude
                if distance <= 5 then
                    KnifeCombat.attackPlayer(targetPlayer.Character)
                end
            end
        end
    end
end

function KnifeCombat.killAll()
    for _, targetPlayer in ipairs(Players:GetPlayers()) do
        if targetPlayer.Character and targetPlayer ~= LocalPlayer then
            KnifeCombat.attackPlayer(targetPlayer.Character)
        end
    end
end

function KnifeCombat.startAutoThrow()
    if KnifeCombat.throwKnifeConnection then
        KnifeCombat.throwKnifeConnection:Disconnect()
    end

    KnifeCombat.throwKnifeConnection = RunService.Heartbeat:Connect(function()
        if KnifeCombat.autoThrowKnife and KnifeCombat.GetMurderer() == LocalPlayer then
            local success = KnifeCombat.throwKnife()
            if success then
                task.wait(1)
            end
        end
    end)
end

function KnifeCombat.stopAutoThrow()
    if KnifeCombat.throwKnifeConnection then
        KnifeCombat.throwKnifeConnection:Disconnect()
        KnifeCombat.throwKnifeConnection = nil
    end
end

function KnifeCombat.startAutoKill()
    if KnifeCombat.killConnection then return end

    KnifeCombat.killConnection = RunService.Heartbeat:Connect(function()
        if KnifeCombat.autoKillEnabled and KnifeCombat.GetMurderer() == LocalPlayer then
            if KnifeCombat.killMode == "Kill Aura" then
                KnifeCombat.killAura()
            elseif KnifeCombat.killMode == "Kill Nearby" then
                KnifeCombat.killNearby()
            elseif KnifeCombat.killMode == "Kill All" then
                KnifeCombat.killAll()
            end
        end
    end)
end

function KnifeCombat.stopAutoKill()
    if KnifeCombat.killConnection then
        KnifeCombat.killConnection:Disconnect()
        KnifeCombat.killConnection = nil
    end
end

function KnifeCombat.updateAuraCircle()
    if KnifeCombat.auraCircle and LocalPlayer.Character then
        local root = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        if root then
            KnifeCombat.auraCircle.CFrame = root.CFrame * CFrame.Angles(0, 0, math.rad(90))
        end
    end
end

function KnifeCombat.createAuraCircle()
    if KnifeCombat.auraCircle then
        KnifeCombat.auraCircle:Destroy()
        KnifeCombat.auraCircle = nil
    end

    if KnifeCombat.showAuraCircle and LocalPlayer.Character then
        local root = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        if root then
            KnifeCombat.auraCircle = Instance.new("Part")
            KnifeCombat.auraCircle.Name = "AuraRange"
            KnifeCombat.auraCircle.Shape = Enum.PartType.Cylinder
            KnifeCombat.auraCircle.Material = Enum.Material.Neon
            KnifeCombat.auraCircle.BrickColor = BrickColor.new("Bright red")
            KnifeCombat.auraCircle.Transparency = 0.7
            KnifeCombat.auraCircle.Anchored = true
            KnifeCombat.auraCircle.CanCollide = false
            KnifeCombat.auraCircle.Size = Vector3.new(1, KnifeCombat.killAuraRadius * 2, KnifeCombat.killAuraRadius * 2)
            KnifeCombat.auraCircle.CFrame = root.CFrame * CFrame.Angles(0, 0, math.rad(90))
            KnifeCombat.auraCircle.Parent = workspace

            if KnifeCombat.auraConnection then
                KnifeCombat.auraConnection:Disconnect()
            end

            KnifeCombat.auraConnection = RunService.Heartbeat:Connect(function()
                KnifeCombat.updateAuraCircle()
            end)
        end
    else
        if KnifeCombat.auraConnection then
            KnifeCombat.auraConnection:Disconnect()
            KnifeCombat.auraConnection = nil
        end
    end
end

function KnifeCombat.checkNearbyPlayer()
    local Character = LocalPlayer.Character
    if not Character then return end
    local Humanoid = Character:FindFirstChildOfClass("Humanoid")
    if not Humanoid then return end

    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Humanoid") then
            local targetRoot = player.Character:FindFirstChild("HumanoidRootPart")
            local localRoot = Character:FindFirstChild("HumanoidRootPart")
            if targetRoot and localRoot then
                if (targetRoot.Position - localRoot.Position).Magnitude <= KnifeCombat.killAuraRadius then
                    local knife = KnifeCombat.GetKnife()
                    if knife and knife.Parent ~= Character then
                        Humanoid:EquipTool(knife)
                    end
                    return
                end
            end
        end
    end
end

function KnifeCombat.KillTarget(targetRoot)
    local knife = KnifeCombat.GetKnife()
    if not knife then return end

    local events = knife:FindFirstChild("Events")
    if events then
        if events:FindFirstChild("KnifeStabbed") then
            events.KnifeStabbed:FireServer()
        end
        if events:FindFirstChild("HandleTouched") then
            events.HandleTouched:FireServer(targetRoot)
        end
    end
end

function KnifeCombat.GetClosestTarget(knifePosition, targets)
    local closest = nil
    local closestDist = math.huge

    for _, target in pairs(targets) do
        local distance = (knifePosition - target.Position).Magnitude
        if distance < closestDist then
            closestDist = distance
            closest = target
        end
    end

    return closest
end

function KnifeCombat.ProcessThrownKnife(knifePart)
    local radius = KnifeCombat.HitboxConfig.Radius
    local multiple = KnifeCombat.HitboxConfig.MultipleTargets

    local targets = {}

    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character then
            local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
            local rootPart = player.Character:FindFirstChild("HumanoidRootPart")

            if rootPart and humanoid and humanoid.Health > 0 then
                local distance = (rootPart.Position - knifePart.Position).Magnitude
                if distance < radius then
                    table.insert(targets, rootPart)
                end
            end
        end
    end

    if #targets == 0 then return end

    if multiple then
        for _, target in pairs(targets) do
            KnifeCombat.KillTarget(target)
        end
    else
        local closest = KnifeCombat.GetClosestTarget(knifePart.Position, targets)
        if closest then
            KnifeCombat.KillTarget(closest)
        end
    end
end

function KnifeCombat.StartHitboxKnifeThrownLoop()
    KnifeCombat.StopHitboxKnifeThrownLoop()

    KnifeCombat.hitboxDescendantConnection = workspace.DescendantAdded:Connect(function(obj)
        local currentTime = tick()
        if currentTime - KnifeCombat.lastHitboxCheck < KnifeCombat.hitboxCheckCooldown then return end
        KnifeCombat.lastHitboxCheck = currentTime

        local knifePart = nil

        if obj.Name == "StuckKnife" and obj:IsA("BasePart") then
            knifePart = obj
        elseif obj.Name == "ThrowingKnife" then
            knifePart = obj:FindFirstChild("KnifeVisual") or obj:FindFirstChildWhichIsA("BasePart")
        elseif obj.Name == "KnifeStickWeld" and obj.Parent then
            knifePart = obj.Parent
        end

        if knifePart then
            KnifeCombat.ProcessThrownKnife(knifePart)
        end
    end)
end

function KnifeCombat.StopHitboxKnifeThrownLoop()
    if KnifeCombat.hitboxHeartbeatConnection then 
        KnifeCombat.hitboxHeartbeatConnection:Disconnect() 
        KnifeCombat.hitboxHeartbeatConnection = nil
    end
    if KnifeCombat.hitboxDescendantConnection then 
        KnifeCombat.hitboxDescendantConnection:Disconnect() 
        KnifeCombat.hitboxDescendantConnection = nil
    end
end

function KnifeCombat.EnableKnifeHitbox()
    if KnifeCombat.HitboxConfig.Enabled then return end
    KnifeCombat.HitboxConfig.Enabled = true
    KnifeCombat.lastHitboxCheck = 0
    KnifeCombat.StartHitboxKnifeThrownLoop()
end

function KnifeCombat.DisableKnifeHitbox()
    if not KnifeCombat.HitboxConfig.Enabled then return end
    KnifeCombat.HitboxConfig.Enabled = false
    KnifeCombat.StopHitboxKnifeThrownLoop()
end

Tabs.Combat:AddSpace({ Height = 20 })
secCombatAuto5 = Tabs.Combat:AddSection("Stab Reach", "solar/widget-2-bold")

secCombatAuto5:AddToggle("StabReachToggle", {
    Title = "Stab Reach",
    Description = "Only work when you spam clicking",
    Default = false,
    Callback = function(DConfiguration)
        KnifeCombat.StabReach.Enabled = DConfiguration
        if DConfiguration then
            KnifeCombat.startStabReach()
        else
            KnifeCombat.stopStabReach()
        end
    end
})

secCombatAuto5:AddSlider("CombatSlider56", {
    Title = "Stab Reach Range",
    Description = "Range for stab reach",
    Min = 1,
    Max = 500,
    Default = 10,
    Rounding = 1,
    Callback = function(value)
        KnifeCombat.StabReach.Radius = tonumber(value)
    end
})

Tabs.Combat:AddSpace({ Height = 20 })
secCombatAuto6 = Tabs.Combat:AddSection("Thrown Knife", "solar/widget-2-bold")

AutoThrowToggle = secCombatAuto6:AddToggle("AutoThrown", {
    Title = "Auto Throw Knife",
    Description = "Automatically throw knife at nearby players",
    Default = false,
    Callback = function(DConfiguration)
        KnifeCombat.autoThrowKnife = DConfiguration
        if DConfiguration then
            KnifeCombat.startAutoThrow()
        else
            KnifeCombat.stopAutoThrow()
        end
        FloatingButtonModule.SetActive(AutoThrowKnifeButton, KnifeCombat.autoThrowKnife, "Auto Throw Knife")
    end
})

secCombatAuto6:AddToggle("AutoThrowKnifeBtnVisibleToggle", {
    Title = "Show Auto Throw Knife Button",
    Default = false,
    Callback = function(DConfiguration)
        FloatingButtonModule.SetVisible(AutoThrowKnifeFrame, DConfiguration)
    end,
})

FBModule:AddSizeInputs(secCombatAuto6, "AutoThrowKnifeBtn", "Auto Throw Knife")

AutoThrowKnifeFrame, AutoThrowKnifeButton = FBModule:Create("AutoThrowKnifeBtn", "Auto Throw Knife", true, function(Btn)
    local newState = not KnifeCombat.autoThrowKnife
    KnifeCombat.autoThrowKnife = newState
    if newState then
        KnifeCombat.startAutoThrow()
    else
        KnifeCombat.stopAutoThrow()
    end
    FloatingButtonModule.SetActive(Btn, newState, "Auto Throw Knife")
    if AutoThrowToggle then
        AutoThrowToggle:SetValue(newState)
    end
end)

secCombatAuto6:AddButton({
    Title = "Manual Throw Knife",
    Description = "Throw knife at nearest player",
    Icon = "target",
    Callback = function()
        if KnifeCombat.GetMurderer() == LocalPlayer then
            KnifeCombat.throwKnife()
        end
    end
})

secCombatAuto6:AddToggle("ThrowKnifeBtnVisibleToggle", {
    Title = "Show Throw Knife Button",
    Default = false,
    Callback = function(DConfiguration)
        ThrowKnifeBtnVisible = DConfiguration
        FloatingButtonModule.SetVisible(ThrowKnifeFrame, DConfiguration)
    end,
})

FBModule:AddSizeInputs(secCombatAuto6, "ThrowKnifeBtn", "THROW KNIFE")

ThrowKnifeFrame, ThrowKnifeButton = FBModule:Create("ThrowKnifeBtn", "THROW KNIFE", false, function()
    if KnifeCombat.GetMurderer() == LocalPlayer then
        KnifeCombat.throwKnife()
    end
end)

secCombatAuto6:AddKeybind("CombatKeybind61", {
    Title = "Throw Knife Keybind",
    Description = "Keybind to throw knife",
    Default = "T",
    Callback = function(key)
        local keyCode = Enum.KeyCode[key]
        if keyCode then
            local connection
            connection = UserInputService.InputBegan:Connect(function(input, gameProcessed)
                if gameProcessed then return end
                if input.KeyCode == keyCode then
                    if KnifeCombat.GetMurderer() == LocalPlayer then
                        KnifeCombat.throwKnife()
                    end
                end
            end)

            return function()
                if connection then
                    connection:Disconnect()
                end
            end
        end
    end
})

secCombatAuto6:AddSpace({ Height = 20 })
secCombatAuto6:AddDropdown("CombatDropdown62", {
    Search = false,
    Title = "Wall Check For Thrown Knife",
    Values = {"Manual Throw Knife", "Auto Throw Knife"},
    Default = {},
    Multi = true,
    AllowNone = true,
    Callback = function(values)
        KnifeCombat.wallCheckType = values
    end
})

Tabs.Combat:AddSpace({ Height = 20 })
secCombatAuto7 = Tabs.Combat:AddSection("Thrown Hitbox", "solar/widget-2-bold")

secCombatAuto7:AddToggle("ThrownHitboxScalerToggle", {
    Title = "Enable Thrown Hitbox",
    Description = "Expands thrown knife hit detection",
    Default = false,
    Callback = function(DConfiguration)
        if DConfiguration then
            KnifeCombat.EnableKnifeHitbox()
        else
            KnifeCombat.DisableKnifeHitbox()
        end
    end
})

secCombatAuto7:AddSlider("CombatSlider65", {
    Title = "Hitbox Radius",
    Description = "Radius for knife hitbox detection",
    Min = 1,
    Max = 500,
    Default = 10,
    Rounding = 1,
    Callback = function(value)
        KnifeCombat.HitboxConfig.Radius = tonumber(value)
    end
})

secCombatAuto7:AddToggle("TSHMT", {
    Title = "Hit Multiple Targets",
    Description = "Hit multiple players with one thrown knife",
    Default = false,
    Type = "Checkbox",
    Callback = function(DConfiguration)
        KnifeCombat.HitboxConfig.MultipleTargets = DConfiguration
    end
})

Tabs.Combat:AddSpace({ Height = 20 })
secCombatAuto8 = Tabs.Combat:AddSection("Auto Kill", "solar/widget-2-bold")

secCombatAuto8:AddToggle("AutoKillToggle", {
    Title = "Auto Kill",
    Flag = "KnifeAutoKillToggle",
    Default = false,
    Callback = function(DConfiguration)
        KnifeCombat.autoKillEnabled = DConfiguration
        if DConfiguration then
            KnifeCombat.startAutoKill()
        else
            KnifeCombat.stopAutoKill()
        end
    end
})

secCombatAuto8:AddToggle("AutoEquipKnife", {
    Title = "Auto Equip Knife",
    Default = false,
    Callback = function(DConfiguration)
        KnifeCombat.autoEquipKnife = DConfiguration
        if DConfiguration then
            KnifeCombat.equipConnection = RunService.Heartbeat:Connect(KnifeCombat.checkNearbyPlayer)
        else
            if KnifeCombat.equipConnection then
                KnifeCombat.equipConnection:Disconnect()
                KnifeCombat.equipConnection = nil
            end
        end
    end
})

secCombatAuto8:AddDropdown("KillModeDropdown", {
    Search = false,
    Title = "Kill Mode",
    Values = {"Kill Aura", "Kill Nearby", "Kill All"},
    Default = "Kill Aura",
    Callback = function(value)
        KnifeCombat.killMode = value
    end
})

secCombatAuto8:AddSlider("KillAuraSlider", {
    Title = "Knife Kill Aura Range",
    Description = "Adjust kill aura radius",
    Min = 1,
    Max = 500,
    Default = 10,
    Rounding = 1,
    Callback = function(value)
        KnifeCombat.killAuraRadius = tonumber(value)
        if KnifeCombat.auraCircle then
            KnifeCombat.auraCircle.Size = Vector3.new(1, KnifeCombat.killAuraRadius * 2, KnifeCombat.killAuraRadius * 2)
        end
    end
})

secCombatAuto8:AddSpace({ Height = 20 })
secCombatAuto8:AddToggle("ShowAuraToggle", {
    Title = "Show Aura Circle",
    Default = false,
    Callback = function(DConfiguration)
        KnifeCombat.showAuraCircle = DConfiguration
        KnifeCombat.createAuraCircle()
    end
})

secCombatAuto8:AddButton({
    Title = "Kill All",
    Description = "Kill all players instantly",
    Icon = "target",
    Callback = function()
        if KnifeCombat.GetMurderer() ~= LocalPlayer then return end
        KnifeCombat.killAll()
    end
})

secVisualsAuto1 = Tabs.Visuals:AddSection("Visual", "solar/widget-2-bold")
secVisualsAuto1:AddDivider()

cameraStretchConnection = nil

function setupCameraStretch()
    cameraStretchConnection = nil
    local stretchHorizontal = 0.80
    local stretchVertical = 0.80
    CameraStretchToggle = secVisualsAuto1:AddToggle("CameraStretchToggle", {
        Title = "Camera Stretch",
        Default = false,
        Callback = function(DConfiguration)
            if DConfiguration then
                if cameraStretchConnection then cameraStretchConnection:Disconnect() end
                cameraStretchConnection = RunService.RenderStepped:Connect(function()
                    local Camera = workspace.CurrentCamera
                    Camera.CFrame = Camera.CFrame * CFrame.new(0, 0, 0, stretchHorizontal, 0, 0, 0, stretchVertical, 0, 0, 0, 1)
                end)
            else
                if cameraStretchConnection then
                    cameraStretchConnection:Disconnect()
                    cameraStretchConnection = nil
                end
            end
        end
    })

    CameraStretchHorizontalInput = secVisualsAuto1:AddInput("CameraStretchHorizontalInput", {
        Title = "Camera Stretch Horizontal",
        Placeholder = "0.80",
        Numeric = true,
        Default = tostring(stretchHorizontal),
        Callback = function(value)
            local num = tonumber(value)
            if num then
                stretchHorizontal = num
                if cameraStretchConnection then
                    cameraStretchConnection:Disconnect()
                    cameraStretchConnection = RunService.RenderStepped:Connect(function()
                        local Camera = workspace.CurrentCamera
                        Camera.CFrame = Camera.CFrame * CFrame.new(0, 0, 0, stretchHorizontal, 0, 0, 0, stretchVertical, 0, 0, 0, 1)
                    end)
                end
            end
        end
    })

    CameraStretchVerticalInput = secVisualsAuto1:AddInput("CameraStretchVerticalInput", {
        Title = "Camera Stretch Vertical",
        Placeholder = "0.80",
        Numeric = true,
        Default = tostring(stretchVertical),
        Callback = function(value)
            local num = tonumber(value)
            if num then
                stretchVertical = num
                if cameraStretchConnection then
                    cameraStretchConnection:Disconnect()
                    cameraStretchConnection = RunService.RenderStepped:Connect(function()
                        local Camera = workspace.CurrentCamera
                        Camera.CFrame = Camera.CFrame * CFrame.new(0, 0, 0, stretchHorizontal, 0, 0, 0, stretchVertical, 0, 0, 0, 1)
                    end)
                end
            end
        end
    })
end
setupCameraStretch()

secVisualsAuto1:AddSpace({ Height = 20 })
FullBrightToggle = secVisualsAuto1:AddToggle("FullBrightToggle", {
    Title = "Full Bright",
    Description = "Ya Like drinking Night Vision while mining in da cave and sceard of creeper blow you up dawg?",
    Default = false,
    Callback = function(DConfiguration)
        FullBright = DConfiguration
        if DConfiguration then
            local Lighting = game:GetService("Lighting")

            originalBrightness = Lighting.Brightness
            originalAmbient = Lighting.Ambient
            originalOutdoorAmbient = Lighting.OutdoorAmbient
            originalColorShiftBottom = Lighting.ColorShift_Bottom
            originalColorShiftTop = Lighting.ColorShift_Top

            function applyFullBright()
                if Lighting.Brightness ~= 1 then
                    Lighting.Brightness = 1
                end
                if Lighting.Ambient ~= Color3.new(1, 1, 1) then
                    Lighting.Ambient = Color3.new(1, 1, 1)
                end
                if Lighting.OutdoorAmbient ~= Color3.new(1, 1, 1) then
                    Lighting.OutdoorAmbient = Color3.new(1, 1, 1)
                end
                if Lighting.ColorShift_Bottom ~= Color3.new(1, 1, 1) then
                    Lighting.ColorShift_Bottom = Color3.new(1, 1, 1)
                end
                if Lighting.ColorShift_Top ~= Color3.new(1, 1, 1) then
                    Lighting.ColorShift_Top = Color3.new(1, 1, 1)
                end
            end

            applyFullBright()

            if fullBrightConnection then
                fullBrightConnection:Disconnect()
            end

            fullBrightConnection = RunService.Heartbeat:Connect(function()
                if FullBright then
                    applyFullBright()
                end
            end)

            fullBrightCharConnection = LocalPlayer.CharacterAdded:Connect(function()
                task.wait(1)
                if FullBright then
                    applyFullBright()
                end
            end)

        else
            if fullBrightConnection then
                fullBrightConnection:Disconnect()
                fullBrightConnection = nil
            end

            if fullBrightCharConnection then
                fullBrightCharConnection:Disconnect()
                fullBrightCharConnection = nil
            end

            if originalBrightness then
                local Lighting = game:GetService("Lighting")
                Lighting.Brightness = originalBrightness
                Lighting.Ambient = originalAmbient
                Lighting.OutdoorAmbient = originalOutdoorAmbient
                Lighting.ColorShift_Bottom = originalColorShiftBottom
                Lighting.ColorShift_Top = originalColorShiftTop
            end
        end
    end
})

FOVSlider = secVisualsAuto1:AddSlider("FOVSlider", {
    Title = "Field of View",
    Min = 1,
    Max = 120,
    Default = workspace.CurrentCamera.FieldOfView,
    Rounding = 1,
    Callback = function(value)
        workspace.CurrentCamera.FieldOfView = tonumber(value)
    end
})

roundTimerEnabled = false
roundTimerGui = nil
roundTimerLabel = nil
roundTimerConnection = nil
clearTweensConnection = nil
loadingMapConnection = nil
roleSelectConnection = nil
victoryConnection = nil
lastTimerText = ""
freezeCheckTime = 0
freezeThreshold = 3
isWaitingForMapVote = false
isLoadingMap = false
roleDisplayTime = 0
roleDisplayDuration = 5
countdownStartTime = 0
isInCountdown = false
timeUpDisplayTime = 0
timeUpDisplayDuration = 3
victoryDisplayTime = 0
victoryDisplayDuration = 5
currentVictoryPlayer = nil
victoryBillboardCheckConnection = nil

function createRoundTimerGui()
    if roundTimerGui then
        roundTimerGui:Destroy()
        roundTimerGui = nil
    end

    roundTimerGui = Instance.new("ScreenGui")
    roundTimerGui.Name = "RoundTimerGui"
    roundTimerGui.IgnoreGuiInset = true
    roundTimerGui.ResetOnSpawn = false
    roundTimerGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    roundTimerGui.Parent = PlayerGui

    local uiScale = Instance.new("UIScale")
    uiScale.Parent = roundTimerGui

    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 200, 0, 40)
    frame.Position = UDim2.new(0.5, -100, 0, 20)
    frame.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    frame.BackgroundTransparency = 0.7
    frame.BorderSizePixel = 0
    frame.Parent = roundTimerGui
    frame:SetAttribute("Locked", true)

    local gradient = Instance.new("UIGradient")
    gradient.Color = (Fluent:GetButtonGradient() or Fluent.ButtonGradients).Background
    gradient.Parent = frame
    task.spawn(function()
        while task.wait(0.03) do
            if not frame.Parent then break end
            local Grad = Fluent:GetButtonGradient() or Fluent.ButtonGradients
            gradient.Rotation = (gradient.Rotation + 1) % 360
            gradient.Color = Grad.Background
        end
    end)

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 15)
    corner.Parent = frame

    local stroke = Instance.new("UIStroke")
    stroke.Thickness = 2
    stroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    stroke.Color = Color3.new(1, 1, 1)
    stroke.Parent = frame
    local gradientstroke = Instance.new("UIGradient")
    gradientstroke.Color = (Fluent:GetButtonGradient() or Fluent.ButtonGradients).Stroke
    gradientstroke.Rotation = 0
    gradientstroke.Parent = stroke
    task.spawn(function()
        while frame.Parent do
            local Grad = Fluent:GetButtonGradient() or Fluent.ButtonGradients
            gradientstroke.Rotation = (gradientstroke.Rotation + 0.5) % 360
            gradientstroke.Color = Grad.Stroke
            task.wait()
        end
    end)

    roundTimerLabel = Instance.new("TextLabel")
    roundTimerLabel.Size = UDim2.new(1, 0, 1, 0)
    roundTimerLabel.BackgroundTransparency = 1
    roundTimerLabel.Text = "Round Timer: --:--"
    roundTimerLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    roundTimerLabel.Font = Enum.Font.RobotoMono
    roundTimerLabel.TextSize = 18
    roundTimerLabel.TextScaled = false
    roundTimerLabel.TextXAlignment = Enum.TextXAlignment.Center
    roundTimerLabel.TextYAlignment = Enum.TextYAlignment.Center
    roundTimerLabel.ZIndex = 2
    roundTimerLabel.Parent = frame
end

function checkVictoryBillboard()
    if tick() - victoryDisplayTime < victoryDisplayDuration then
        return true
    end

    for _, player in ipairs(Players:GetPlayers()) do
        if player.Character then
            local humanoidRootPart = player.Character:FindFirstChild("HumanoidRootPart")
            if humanoidRootPart then
                local victoryBillboard = humanoidRootPart:FindFirstChild("VictoryBillboard")
                if victoryBillboard and victoryBillboard:IsA("BillboardGui") then
                    currentVictoryPlayer = player
                    victoryDisplayTime = tick()
                    roundTimerLabel.Text = player.Name .. " Win"
                    return true
                end
            end
        end
    end

    if currentVictoryPlayer and tick() - victoryDisplayTime >= victoryDisplayDuration then
        currentVictoryPlayer = nil
    end

    return false
end

function checkRoleSelectorCountdown()
    local mainGUI = PlayerGui:FindFirstChild("MainGUI")
    if not mainGUI then return false end

    local gameFrame = mainGUI:FindFirstChild("Game")
    if not gameFrame then return false end

    local roleSelector = gameFrame:FindFirstChild("RoleSelector")
    if not roleSelector or not roleSelector.Visible then return false end

    local roleText = roleSelector:FindFirstChild("Role")
    if not roleText then return false end

    local text = roleText.Text
    local number = tonumber(text)

    if number then
        roundTimerLabel.Text = "Round start in " .. number
        countdownStartTime = tick()
        isInCountdown = true
        return true
    end

    return false
end

function updateRoundTimer()
    if not roundTimerEnabled or not roundTimerLabel then return end

    if checkVictoryBillboard() then
        return
    end

    if tick() - timeUpDisplayTime < timeUpDisplayDuration then
        return
    end

    if isInCountdown then
        if tick() - countdownStartTime >= 1 then
            isInCountdown = false
        else
            return
        end
    end

    if checkRoleSelectorCountdown() then
        return
    end

    if tick() - roleDisplayTime < roleDisplayDuration then
        return
    end

    if isLoadingMap then
        roundTimerLabel.Text = "Loading map..."
        return
    end

    if isWaitingForMapVote then
        roundTimerLabel.Text = "Waiting for map vote"
        return
    end

    local timerPart = workspace:FindFirstChild("RoundTimerPart")
    if not timerPart then
        roundTimerLabel.Text = "Round Timer: --:--"
        lastTimerText = ""
        freezeCheckTime = tick()
        return
    end

    local surfaceGui = timerPart:FindFirstChildOfClass("SurfaceGui")
    if not surfaceGui then
        roundTimerLabel.Text = "Round Timer: --:--"
        lastTimerText = ""
        freezeCheckTime = tick()
        return
    end

    local timerLabel = surfaceGui:FindFirstChild("Timer")
    if not timerLabel then
        roundTimerLabel.Text = "Round Timer: --:--"
        lastTimerText = ""
        freezeCheckTime = tick()
        return
    end

    local currentText = timerLabel.Text
    local displayText = "Round Timer: " .. currentText

    if currentText == "1s" or currentText == "0s" then
        roundTimerLabel.Text = "Time's up"
        timeUpDisplayTime = tick()
        return
    end

    if currentText == lastTimerText then
        if tick() - freezeCheckTime >= freezeThreshold then
            roundTimerLabel.Text = displayText
            return
        end
    else
        lastTimerText = currentText
        freezeCheckTime = tick()
    end

    roundTimerLabel.Text = displayText
end

function setupClearTweensListener()
    if clearTweensConnection then
        clearTweensConnection:Disconnect()
        clearTweensConnection = nil
    end

    local ClientTweenEvents = ReplicatedStorage:WaitForChild("ClientTweenEvents")
    local ClearTweens = ClientTweenEvents:WaitForChild("ClearTweens")

    clearTweensConnection = ClearTweens.OnClientEvent:Connect(function(...)
        if roundTimerEnabled and roundTimerLabel then
            isWaitingForMapVote = true
            isLoadingMap = false
            lastTimerText = ""
            freezeCheckTime = tick()
            timeUpDisplayTime = 0
            victoryDisplayTime = 0
            currentVictoryPlayer = nil
        end
    end)
end

function setupLoadingMapListener()
    if loadingMapConnection then
        loadingMapConnection:Disconnect()
        loadingMapConnection = nil
    end

    local Remotes = ReplicatedStorage:WaitForChild("Remotes")
    local Gameplay = Remotes:WaitForChild("Gameplay")
    local LoadingMap = Gameplay:WaitForChild("LoadingMap")

    loadingMapConnection = LoadingMap.OnClientEvent:Connect(function(mapName)
        if roundTimerEnabled and roundTimerLabel then
            isLoadingMap = true
            isWaitingForMapVote = false
            lastTimerText = ""
            freezeCheckTime = tick()
            timeUpDisplayTime = 0
            victoryDisplayTime = 0
            currentVictoryPlayer = nil
        end
    end)
end

function setupRoleSelectListener()
    if roleSelectConnection then
        roleSelectConnection:Disconnect()
        roleSelectConnection = nil
    end

    local RoleSelect = ReplicatedStorage.Remotes.Gameplay.RoleSelect
    roleSelectConnection = RoleSelect.OnClientEvent:Connect(function(role, ...)
        if roundTimerEnabled and roundTimerLabel then
            local roleName = role or "Unknown"
            roundTimerLabel.Text = "Your role is " .. roleName
            roleDisplayTime = tick()
            isLoadingMap = false
            isWaitingForMapVote = false
            timeUpDisplayTime = 0
            victoryDisplayTime = 0
            currentVictoryPlayer = nil
        end
    end)
end

function startVictoryBillboardCheck()
    if victoryBillboardCheckConnection then
        victoryBillboardCheckConnection:Disconnect()
        victoryBillboardCheckConnection = nil
    end

    victoryBillboardCheckConnection = RunService.Heartbeat:Connect(function()
        if roundTimerEnabled and roundTimerLabel then
            checkVictoryBillboard()
        end
    end)
end

function startRoundTimer()
    if roundTimerConnection then return end

    createRoundTimerGui()
    setupClearTweensListener()
    setupLoadingMapListener()
    setupRoleSelectListener()
    startVictoryBillboardCheck()

    lastTimerText = ""
    freezeCheckTime = tick()
    isWaitingForMapVote = false
    isLoadingMap = false
    roleDisplayTime = 0
    countdownStartTime = 0
    isInCountdown = false
    timeUpDisplayTime = 0
    victoryDisplayTime = 0
    currentVictoryPlayer = nil

    roundTimerConnection = RunService.Heartbeat:Connect(function()
        updateRoundTimer()
    end)
end

function stopRoundTimer()
    if roundTimerConnection then
        roundTimerConnection:Disconnect()
        roundTimerConnection = nil
    end

    if clearTweensConnection then
        clearTweensConnection:Disconnect()
        clearTweensConnection = nil
    end

    if loadingMapConnection then
        loadingMapConnection:Disconnect()
        loadingMapConnection = nil
    end

    if roleSelectConnection then
        roleSelectConnection:Disconnect()
        roleSelectConnection = nil
    end

    if victoryBillboardCheckConnection then
        victoryBillboardCheckConnection:Disconnect()
        victoryBillboardCheckConnection = nil
    end

    if roundTimerGui then
        roundTimerGui:Destroy()
        roundTimerGui = nil
        roundTimerLabel = nil
    end

    lastTimerText = ""
    freezeCheckTime = 0
    isWaitingForMapVote = false
    isLoadingMap = false
    roleDisplayTime = 0
    countdownStartTime = 0
    isInCountdown = false
    timeUpDisplayTime = 0
    victoryDisplayTime = 0
    currentVictoryPlayer = nil
end

RoundTimerToggle = secVisualsAuto1:AddToggle("RoundTimerToggle", {
    Title = "Round Timer Display",
    Description = "Show round timer in top middle of screen",
    Default = false,
    Callback = function(DConfiguration)
        roundTimerEnabled = DConfiguration
        if DConfiguration then
            startRoundTimer()
        else
            stopRoundTimer()
        end
    end
})

Players.PlayerRemoving:Connect(function(leavingPlayer)
    if leavingPlayer == LocalPlayer then
        stopRoundTimer()
    end
end)

xRay = false

function ApplyXray(DConfiguration)
    xRay = DConfiguration
    for _, part in pairs(workspace:GetDescendants()) do
        if part:IsA("BasePart") and not part:IsDescendantOf(LocalPlayer.Character) then
            part.LocalTransparencyModifier = DConfiguration and 0.7 or 0
        end
    end
end

XrayToggle = secVisualsAuto1:AddToggle("Xray", {
    Title = "X-ray Vision",
    Compact = true,
    Callback = function(DConfiguration)
        ApplyXray(DConfiguration)
        FloatingButtonModule.SetActive(XrayFloatButton, DConfiguration, "X-ray Vision")
    end
})

secVisualsAuto1:AddToggle("XrayBtnVisibleToggle", {
    Title = "Show X-ray Vision Button",
    Default = false,
    Callback = function(DConfiguration)
        FloatingButtonModule.SetVisible(XrayFrame, DConfiguration)
    end,
})

FBModule:AddSizeInputs(secVisualsAuto1, "XrayBtn", "X-ray Vision")

XrayFrame, XrayFloatButton = FBModule:Create("XrayBtn", "X-ray Vision", true, function(Btn)
    local newState = not xRay
    ApplyXray(newState)
    FloatingButtonModule.SetActive(Btn, newState, "X-ray Vision")
    if XrayToggle then
        XrayToggle:SetValue(newState)
    end
end)

secVisualsAuto1:AddButton({
    Title = "Remove Footsteps",
    Callback = function()
        local Players = game:GetService("Players")
        local player = Players.LocalPlayer
        local character = player.Character or player.CharacterAdded:Wait()
        character:WaitForChild("Footsteps").Disabled = true
        if workspace:FindFirstChild("Footsteps") then workspace.Footsteps:Destroy() end 
    end
})

StuckKnifeRemoved = false
secVisualsAuto1:AddSpace({ Height = 20 })
secVisualsAuto1:AddButton({
    Title = "Remove StuckKnife",
    Callback = function()
        if workspace:FindFirstChild("StuckKnife") then workspace.StuckKnife:Destroy() end
    end
})

secVisualsAuto1:AddButton({
    Title = "Auto Remove Dead Body",
    Callback = function()
        for _, obj in pairs(workspace:GetDescendants()) do
            if obj.Name == "Raggy" and obj:IsA("Model") then
                pcall(function()
                    obj:Destroy()
                end)
            end
        end
    end
})

secVisualsAuto1:AddToggle("Anti CoinLag", {
    Title = "Disable CoinVisualizer",
    Callback = function(DConfiguration)
        LocalPlayer.PlayerScripts.CoinVisualizer.Disabled = DConfiguration
    end
})

SpectateService = require(ReplicatedStorage:WaitForChild("Modules"):WaitForChild("SpectateService"))

originalToggle = SpectateService.ToggleSpectate
originalSetSpectating = SpectateService.SetSpectating

CurrentRoundClient = require(ReplicatedStorage:WaitForChild("Modules"):WaitForChild("CurrentRoundClient"))

forceSpectate = false
customSpectateEnabled = false
customSpectateTarget = nil
customSpectateConnection = nil
forceSpectateConnection = nil

function forceDeadState()
    local playerData = CurrentRoundClient.PlayerData[LocalPlayer.Name]
    if playerData then
        playerData.Dead = true
    end
end

function restoreAliveState()
    local playerData = CurrentRoundClient.PlayerData[LocalPlayer.Name]
    if playerData then
        playerData.Dead = false
    end
end

function stopForceSpectate()
    if forceSpectateConnection then
        forceSpectateConnection:Disconnect()
        forceSpectateConnection = nil
    end
    restoreAliveState()
    pcall(function()
        SpectateService.CancelSpectate()
    end)
    pcall(function()
        originalSetSpectating(SpectateService, false)
    end)
end

function startForceSpectate()
    forceDeadState()
    task.wait(0.1)
    pcall(function()
        SpectateService.ToggleSpectate()
    end)
    task.wait(0.1)
    pcall(function()
        SpectateService.SetSpectating(SpectateService, true)
    end)
end

function SpectateService.ToggleSpectate()
    if forceSpectate then
        forceDeadState()
    end
    pcall(function()
        originalToggle(SpectateService)
    end)
end

function SpectateService.SetSpectating(_, enabled)
    if enabled and forceSpectate then
        forceDeadState()
    elseif not enabled then
        restoreAliveState()
    end
    return originalSetSpectating(SpectateService, enabled)
end

function autoSpectate()
    if forceSpectate then
        forceDeadState()
        task.wait(0.1)
        pcall(function()
            SpectateService.ToggleSpectate()
        end)
        task.wait(0.1)
        pcall(function()
            SpectateService.SetSpectating(SpectateService, true)
        end)
    end
end

function startCustomSpectate(player)
    if customSpectateConnection then
        customSpectateConnection:Disconnect()
        customSpectateConnection = nil
    end

    if not player or not player.Character then
        return false
    end

    local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
    if not humanoid then
        return false
    end

    workspace.CurrentCamera.CameraSubject = humanoid

    customSpectateConnection = RunService.RenderStepped:Connect(function()
        if not customSpectateEnabled or not customSpectateTarget then
            return
        end

        if not customSpectateTarget.Character or not customSpectateTarget.Character:FindFirstChildOfClass("Humanoid") then
            return
        end

        local targetHumanoid = customSpectateTarget.Character:FindFirstChildOfClass("Humanoid")
        if targetHumanoid and workspace.CurrentCamera.CameraSubject ~= targetHumanoid then
            workspace.CurrentCamera.CameraSubject = targetHumanoid
        end
    end)

    return true
end

function stopCustomSpectate()
    if customSpectateConnection then
        customSpectateConnection:Disconnect()
        customSpectateConnection = nil
    end

    customSpectateEnabled = false
    customSpectateTarget = nil

    if LocalPlayer.Character then
        local humanoid = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
        if humanoid then
            workspace.CurrentCamera.CameraSubject = humanoid
        else
            workspace.CurrentCamera.CameraSubject = nil
        end
    else
        workspace.CurrentCamera.CameraSubject = nil
    end
end

function getPlayerList()
    local players = {}
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            table.insert(players, player.Name)
        end
    end
    if #players == 0 then
        players = {"None"}
    end
    return players
end

selectedPlayerForSpectate = "None"
customSpectateToggleObject = nil
spectatePlayerDropdownObject = nil

forceSpectateToggleObject = secVisualsAuto1:AddToggle("ForceSpectateToggle", {
    Title = "Force Spectate",
    Description = "Force Spectate While In Round Started",
    Default = false,
    Callback = function(DConfiguration)
        forceSpectate = DConfiguration
        if DConfiguration then
            startForceSpectate()
            forceSpectateConnection = LocalPlayer.CharacterAdded:Connect(function()
                task.wait(1)
                if forceSpectate then
                    startForceSpectate()
                end
            end)
        else
            if forceSpectateConnection then
                forceSpectateConnection:Disconnect()
                forceSpectateConnection = nil
            end
            stopForceSpectate()
        end
    end
})


customSpectateToggleObject = secVisualsAuto1:AddToggle("CustomSpectateToggle", {
    Title = "Custom Spectate Selected Player",
    Description = "Spectate any player without game logic",
    Default = false,
    Callback = function(DConfiguration)
        if DConfiguration then
            if selectedPlayerForSpectate and selectedPlayerForSpectate ~= "None" then
                local targetPlayer = Players:FindFirstChild(selectedPlayerForSpectate)
                if targetPlayer and targetPlayer.Character then
                    customSpectateEnabled = true
                    customSpectateTarget = targetPlayer
                    startCustomSpectate(targetPlayer)
                else
                    customSpectateToggleObject:SetValue(false)
                end
            else
                customSpectateToggleObject:SetValue(false)
            end
        else
            stopCustomSpectate()
        end
    end
})

secVisualsAuto1:AddSpace({ Height = 20 })
spectatePlayerDropdownObject = secVisualsAuto1:AddDropdown("SpectatePlayerDropdown", {
    DropdownOutsideWindow = true,
    Title = "Select Player To Spectate",
    Values = getPlayerList(),
    Default = "None",
    Callback = function(value)
        selectedPlayerForSpectate = value

        if value == "None" or value == nil then
            if customSpectateEnabled then
                customSpectateToggleObject:SetValue(false)
                stopCustomSpectate()
            end
            return
        end

        if customSpectateEnabled then
            local targetPlayer = Players:FindFirstChild(value)
            if targetPlayer then
                customSpectateTarget = targetPlayer
                startCustomSpectate(targetPlayer)
            else
                if customSpectateEnabled then
                    customSpectateToggleObject:SetValue(false)
                    stopCustomSpectate()
                end
            end
        end
    end
})

function updateSpectateDropdown()
    if spectatePlayerDropdownObject and spectatePlayerDropdownObject.Refresh then
        local currentValue = selectedPlayerForSpectate
        local players = getPlayerList()

        if currentValue ~= "None" and not Players:FindFirstChild(currentValue) then
            currentValue = "None"
            selectedPlayerForSpectate = "None"
            if customSpectateEnabled then
                customSpectateToggleObject:SetValue(false)
                stopCustomSpectate()
            end
        end

        if #players == 0 then
            players = {"None"}
            currentValue = "None"
            selectedPlayerForSpectate = "None"
            if customSpectateEnabled then
                customSpectateToggleObject:SetValue(false)
                stopCustomSpectate()
            end
        end

        spectatePlayerDropdownObject:SetValues(players)
        spectatePlayerDropdownObject:SetValue(currentValue)
    end
end

Players.PlayerAdded:Connect(function()
    task.wait(0.1)
    updateSpectateDropdown()
end)

Players.PlayerRemoving:Connect(function(player)
    task.wait(0.1)
    if customSpectateEnabled and customSpectateTarget == player then
        customSpectateToggleObject:SetValue(false)
        stopCustomSpectate()
        selectedPlayerForSpectate = "None"
    end
    updateSpectateDropdown()
end)

LocalPlayer.CharacterAdded:Connect(function()
    if not customSpectateEnabled then
        if LocalPlayer.Character then
            local humanoid = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
            if humanoid then
                workspace.CurrentCamera.CameraSubject = humanoid
            end
        end
    end
end)

secVisualsAuto1:AddButton({
    Title = "Shit Render", 
    Callback = function()
        Lighting = game:GetService("Lighting")
        Terrain = workspace:FindFirstChildOfClass("Terrain")
        Lighting.GlobalShadows = false
        Lighting.FogEnd = 1e10
        Lighting.Brightness = 1

        if Terrain then
            Terrain.WaterWaveSize = 0
            Terrain.WaterWaveSpeed = 0
            Terrain.WaterReflectance = 0
            Terrain.WaterTransparency = 1
        end

        for _, obj in ipairs(workspace:GetDescendants()) do
            if obj:IsA("BasePart") then
                obj.Material = Enum.Material.Plastic
                obj.Reflectance = 0
            elseif obj:IsA("Decal") or obj:IsA("Texture") then
                obj:Destroy()
            elseif obj:IsA("ParticleEmitter") or obj:IsA("Trail") then
                obj:Destroy()
            elseif obj:IsA("PointLight") or obj:IsA("SpotLight") or obj:IsA("SurfaceLight") then
                obj:Destroy()
            end
        end

        for _, player in ipairs(Players:GetPlayers()) do
            local char = player.Character
            if char then
                for _, part in ipairs(char:GetDescendants()) do
                    if part:IsA("Accessory") or part:IsA("Clothing") then
                        part:Destroy()
                    end
                end
            end
        end
    end
})

function spawnWeapon(name)
    local DataBase, PlayerData = require(ReplicatedStorage.Database.Sync.Item),
    require(ReplicatedStorage.Modules.ProfileData)
    local newOwned = {}
    newOwned[name] = 1
    local PlayerWeapons = PlayerData.Weapons
    RunService:BindToRenderStep("InventoryUpdate", 0, function()
        PlayerWeapons.Owned = newOwned
    end)
    LocalPlayer.Character:BreakJoints()
end

WeaponOwnedRange = { min = 1, max = 100000 }

Tabs.Visuals:AddSpace({ Height = 20 })
secVisualsAuto2 = Tabs.Visuals:AddSection("Weapon Visuals", "solar/widget-2-bold")

secVisualsAuto2:AddParagraph({
    Title = "VISUAL WARNING",
    Content = "ALL items are in fact visual and not real you do not get to keep any of the items after rejoining the game they are only for show and do not actually exist ",
    Image = "eye"
})

secVisualsAuto2:AddSlider("VisualsSlider92", {
    Title = "Min",
    Min = 1,
    Max = 100000,
    Default = 1,
    Rounding = 0,
    Compact = true,
    Callback = function(value) WeaponOwnedRange.min = tonumber(value) or 1 end
})

secVisualsAuto2:AddSlider("VisualsSlider93", {
    Title = "Max",
    Min = 1,
    Max = 100000,
    Default = 150,
    Rounding = 0,
    Compact = true,
    Callback = function(value) WeaponOwnedRange.max = tonumber(value) or 150 end
})

secVisualsAuto2:AddButton({
    Title = "spawn random Godlys (if they don't spawn reset ",
    Compact = true,
    Callback = function()
        local DataBase = require(ReplicatedStorage.Database.Sync.Item)
        local PlayerData = require(ReplicatedStorage.Modules.ProfileData)
        local newOwned = {}
        for i, v in pairs(DataBase) do
            newOwned[i] = math.random(WeaponOwnedRange.min, WeaponOwnedRange.max)
        end
        RunService:BindToRenderStep("InventoryUpdate", 0, function()
            PlayerData.Weapons.Owned = newOwned
        end)
        Fluent:Notify({ Title = "Visuals Enabled", Content = "Fake counts activated!", Duration = 2 })
    end
})

Tabs.Visuals:AddSpace({ Height = 20 })
secVisualsAuto3 = Tabs.Visuals:AddSection("Item Spawner", "solar/widget-2-bold")

secVisualsAuto3:AddInput("VisualsInput96", {
    Title = "Weapon Name",
    Placeholder = "Enter weapon name..",
    Compact = true,
    Callback = function(inputText)
        if inputText and inputText ~= "" then
            spawnWeapon(inputText)
            Fluent:Notify({ Title = "Weapon Spawned", Content = inputText.." added!", Duration = 2 })
        end
    end
})

Tabs.Visuals:AddSpace({ Height = 20 })
secVisualsAuto4 = Tabs.Visuals:AddSection("weapon dupe ", "solar/widget-2-bold")

Tabs.Visuals:AddSpace({ Height = 20 })
secVisualsAuto5 = Tabs.Visuals:AddSection("Duplication Options", "solar/widget-2-bold")

secVisualsAuto5:AddInput("VisualsInput99", {
    Title = "Duplication Multiplier",
    Placeholder = "Enter multiplier (e.g., 2, 3)",
    Compact = true,
    Callback = function(inputText)
        local multiplier = tonumber(inputText)
        if multiplier and multiplier > 0 then
            DupeMultiplier = multiplier
            Fluent:Notify({ Title = "Multiplier Set", Content = "Duplication multiplier set to x" .. multiplier, Duration = 2 })
        else
            Fluent:Notify({ Title = "Invalid Multiplier", Content = "Please enter a valid multiplier (greater than 0).", Duration = 2 })
        end
    end
})

secVisualsAuto5:AddInput("VisualsInput100", {
    Title = "Specific Item to Duplicate",
    Placeholder = "Enter item name to dupe (e.g., Christmas Knife)",
    Compact = true,
    Callback = function(inputText)
        DupeSpecificItem = inputText
        Fluent:Notify({ Title = "Item Set", Content = "Specific item set to duplicate: " .. inputText, Duration = 2 })
    end
})

secVisualsAuto5:AddButton({
    Title = "Duplicate Inventory",
    Compact = true,
    Callback = function()
        local UIPath

        if PlayerGui.MainGUI.Game:FindFirstChild("Inventory") ~= nil then
            UIPath = PlayerGui.MainGUI.Game.Inventory.Main
        else
            UIPath = PlayerGui.MainGUI.Lobby.Screens.Inventory.Main
        end

        function VisualDupe()
            local multiplier = DupeMultiplier or 2
            local specificItem = DupeSpecificItem

            for _, item in pairs(UIPath.Weapons.Items.Container:GetChildren()) do
                for _, weapon in pairs(item.Container:GetChildren()) do
                    if weapon:IsA("Frame") then
                        local itemName = weapon.ItemName.Label.Text
                        if (not specificItem or itemName == specificItem) and itemName ~= "Default Knife" and itemName ~= "Default Gun" then
                            local amount = weapon.Container.Amount.Text
                            if amount == "" or amount == "None" then
                                weapon.Container.Amount.Text = "x" .. tostring(multiplier)
                            else
                                local num = tonumber(amount:match("x(%d+)"))
                                if num then
                                    weapon.Container.Amount.Text = "x" .. tostring(num * multiplier)
                                end
                            end
                        end
                    end
                end
            end

            for _, pet in pairs(UIPath.Pets.Items.Container.Current.Container:GetChildren()) do
                if pet:IsA("Frame") then
                    local amount = pet.Container.Amount.Text
                    if amount == "" or amount == "None" then
                        pet.Container.Amount.Text = "x" .. tostring(multiplier)
                    else
                        local num = tonumber(amount:match("x(%d+)"))
                        if num then
                            pet.Container.Amount.Text = "x" .. tostring(num * multiplier)
                        end
                    end
                end
            end
        end

        VisualDupe()

        Fluent:Notify({ Title = "Inventory Visual Duplication", Content = "Your inventory has been visually duplicated!", Duration = 2 })
    end
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
        pcall(function() makefolder("GoonWares Skyboxes") end)
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

Tabs.Visuals:AddSpace({ Height = 20 })
SecSkyboxChanger = Tabs.Visuals:AddSection("Skybox Changer", "solar/sun-bold")

BuiltInSkyboxNames = {}
for skyboxName in pairs(BuiltInSkyboxes) do
    table.insert(BuiltInSkyboxNames, skyboxName)
end
table.sort(BuiltInSkyboxNames)

SelectedBuiltInSkybox = BuiltInSkyboxNames[1]
SecSkyboxChanger:AddDropdown("BuiltInSkyboxDropdown", {
    DropdownOutsideWindow = true,
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

SecSkyboxChanger:AddParagraph({
    Title = "Built-in Skybox Info",
    Content = "Your executors must have a write file/read file system, and for some skyboxes you will have to wait about 20 seconds to fully load the skybox."
})

Tabs.Visuals:AddSpace({ Height = 20 })
SecCustomSkybox = Tabs.Visuals:AddSection("Custom Skybox", "solar/gallery-bold")

CustomSkyboxInputs = { Lf = "", Rt = "", Up = "", Dn = "", Ft = "", Bk = "" }

function ResolveSkyboxInput(value, faceName)
    value = tostring(value or ""):gsub("^%s*(.-)%s*$", "%1")
    if value == "" then return nil end
    if value:match("^rbxassetid://") then
        return value
    elseif value:match("^%d+$") then
        return "rbxassetid://" .. value
    elseif value:match("^https?://") then
        local ok = pcall(function() makefolder("GoonWares Custom Skybox") end)
        local path = "GoonWares Custom Skybox/" .. faceName .. ".png"
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
    Content = "You can upload 6 cube skyboxes to Roblox create or create 6 cubemaps as direct download links then enter the url or rbxassetid into the 6 inputs must be in the direction of the cube"
})

gunEspElements = {}
roleEspElements = {}

gunBoxesEnabled = false
gunNamesEnabled = false
gunDistanceEnabled = false
gunHighlightsEnabled = false
gunBoxType = "2D"
roleSettings = {}
for roleName in pairs(RoleList) do
    roleSettings[roleName] = {
        boxesEnabled = false,
        namesEnabled = false,
        distanceEnabled = false,
        highlightsEnabled = false,
        boxType = "2D"
    }
end

isRendering = true
windowFocused = true

frameSkipCounter = 0
FRAME_SKIP = 3
gunUpdateInterval = 0.3
lastGunUpdate = 0
lastRoleUpdate = 0
roleUpdateInterval = 0.2
lastGunScanTime = 0
cachedGuns = {}

function getDistanceFromCamera(targetPosition)
    local camera = workspace.CurrentCamera
    if not camera then return 0 end
    return (targetPosition - camera.CFrame.Position).Magnitude
end

function calculateBoxScale(distance)
    if distance <= 17 then
        return 1
    else
        local scale = 17 / distance
        return math.max(scale, 0.3)
    end
end

roundDataCache = nil
roundDataCacheTime = 0
roundDataCacheDuration = 0.5

function getRoundData()
    local currentTime = tick()
    if roundDataCache and (currentTime - roundDataCacheTime) < roundDataCacheDuration then
        return roundDataCache
    end
    
    local currentRoundModule = ReplicatedStorage:FindFirstChild("Modules") and ReplicatedStorage.Modules:FindFirstChild("CurrentRoundClient")
    if currentRoundModule then
        local success, roundData = pcall(function()
            return require(currentRoundModule)
        end)
        if success and roundData and roundData.PlayerData then
            roundDataCache = roundData
            roundDataCacheTime = currentTime
            return roundData
        end
    end
    return nil
end

function getPlayerRoleAndStatus(player)
    local roundData = getRoundData()
    if roundData then
        local playerData = roundData.PlayerData[player.Name]
        if playerData then
            return playerData.Role, playerData.Dead
        end
    end
    return nil, nil
end

function getRoleColor(role, isDead)
    if isDead then
        return Color3.fromRGB(255, 255, 255)
    end
    return RoleList[role] or Color3.fromRGB(200, 200, 200)
end

function getRoleHexColor(role)
    local color = RoleList[role]
    if not color then return "#C8C8C8" end
    return string.format("#%02X%02X%02X", math.floor(color.R * 255), math.floor(color.G * 255), math.floor(color.B * 255))
end

function create3DBox(character, color, size)
    local rootPart = character:FindFirstChild("HumanoidRootPart")
    if not rootPart then return nil end

    local folderName = "ESP_3DBox"
    local folder = character:FindFirstChild(folderName)
    if folder then
        folder:Destroy()
    end

    folder = Instance.new("Folder")
    folder.Name = folderName
    folder.Parent = character

    size = size or Vector3.new(4, 5, 3)
    local offsetX = size.X / 2
    local offsetY = size.Y / 2
    local offsetZ = size.Z / 2

    local edges = {
        {Vector3.new(0, offsetY, offsetZ), Vector3.new(size.X, 0.1, 0.1), "TopFront"},
        {Vector3.new(0, offsetY, -offsetZ), Vector3.new(size.X, 0.1, 0.1), "TopBack"},
        {Vector3.new(-offsetX, offsetY, 0), Vector3.new(0.1, 0.1, size.Z), "TopLeft"},
        {Vector3.new(offsetX, offsetY, 0), Vector3.new(0.1, 0.1, size.Z), "TopRight"},
        {Vector3.new(0, -offsetY, offsetZ), Vector3.new(size.X, 0.1, 0.1), "BottomFront"},
        {Vector3.new(0, -offsetY, -offsetZ), Vector3.new(size.X, 0.1, 0.1), "BottomBack"},
        {Vector3.new(-offsetX, -offsetY, 0), Vector3.new(0.1, 0.1, size.Z), "BottomLeft"},
        {Vector3.new(offsetX, -offsetY, 0), Vector3.new(0.1, 0.1, size.Z), "BottomRight"},
        {Vector3.new(-offsetX, 0, offsetZ), Vector3.new(0.1, size.Y, 0.1), "FrontLeft"},
        {Vector3.new(offsetX, 0, offsetZ), Vector3.new(0.1, size.Y, 0.1), "FrontRight"},
        {Vector3.new(-offsetX, 0, -offsetZ), Vector3.new(0.1, size.Y, 0.1), "BackLeft"},
        {Vector3.new(offsetX, 0, -offsetZ), Vector3.new(0.1, size.Y, 0.1), "BackRight"}
    }

    for _, edge in ipairs(edges) do
        local position = edge[1]
        local boxSize = edge[2]
        local name = edge[3]

        local adornment = Instance.new("BoxHandleAdornment")
        adornment.Name = name
        adornment.Adornee = rootPart
        adornment.Size = boxSize
        adornment.CFrame = CFrame.new(position)
        adornment.Color3 = color
        adornment.Transparency = 0.2
        adornment.ZIndex = 10
        adornment.AlwaysOnTop = true
        adornment.Visible = true
        adornment.Parent = folder
    end

    return folder
end

function update3DBoxColor(character, color)
    local folder = character:FindFirstChild("ESP_3DBox")
    if folder then
        for _, adornment in ipairs(folder:GetChildren()) do
            if adornment:IsA("BoxHandleAdornment") then
                adornment.Color3 = color
            end
        end
    end
end

function remove3DBox(character)
    local folder = character:FindFirstChild("ESP_3DBox")
    if folder then
        folder:Destroy()
    end
end

function createBillboard(character, name, color)
    local existing = character:FindFirstChild("ESP_Billboard")
    if existing then
        existing:Destroy()
    end

    local billboard = Instance.new("BillboardGui")
    billboard.Name = "ESP_Billboard"

    local rootPart = character:FindFirstChild("HumanoidRootPart")
    if rootPart then
        billboard.Adornee = rootPart
        billboard.Parent = rootPart
    else
        billboard.Adornee = character
        billboard.Parent = character
    end

    billboard.AlwaysOnTop = true
    billboard.Size = UDim2.new(0, 200, 0, 50)
    billboard.StudsOffset = Vector3.new(0, 3, 0)
    billboard.ClipsDescendants = false
    billboard.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    billboard.Active = true

    local mainFrame = Instance.new("Frame")
    mainFrame.Name = "MainFrame"
    mainFrame.Size = UDim2.new(1, 0, 1, 0)
    mainFrame.BackgroundTransparency = 1
    mainFrame.Parent = billboard

    local nameLabel = Instance.new("TextLabel")
    nameLabel.Name = "NameLabel"
    nameLabel.Size = UDim2.new(1, 0, 0, 20)
    nameLabel.Position = UDim2.new(0, 0, 0, 0)
    nameLabel.BackgroundTransparency = 1
    nameLabel.Text = name
    nameLabel.TextColor3 = color
    nameLabel.TextSize = 14
    nameLabel.Font = Enum.Font.GothamSemibold
    nameLabel.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
    nameLabel.TextStrokeTransparency = 0.3
    nameLabel.TextXAlignment = Enum.TextXAlignment.Center
    nameLabel.TextYAlignment = Enum.TextYAlignment.Bottom
    nameLabel.Parent = mainFrame

    local distanceLabel = Instance.new("TextLabel")
    distanceLabel.Name = "DistanceLabel"
    distanceLabel.Size = UDim2.new(1, 0, 0, 16)
    distanceLabel.Position = UDim2.new(0, 0, 0, 20)
    distanceLabel.BackgroundTransparency = 1
    distanceLabel.Text = ""
    distanceLabel.TextColor3 = color
    distanceLabel.TextSize = 12
    distanceLabel.Font = Enum.Font.Gotham
    distanceLabel.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
    distanceLabel.TextStrokeTransparency = 0.3
    distanceLabel.TextXAlignment = Enum.TextXAlignment.Center
    distanceLabel.TextYAlignment = Enum.TextYAlignment.Top
    distanceLabel.Parent = mainFrame

    return {
        billboard = billboard,
        nameLabel = nameLabel,
        distanceLabel = distanceLabel
    }
end

function updateBillboard(billboardData, name, distance, color)
    if not billboardData then return end

    if name then
        billboardData.nameLabel.Text = name
        billboardData.nameLabel.TextColor3 = color
    end

    if distance then
        billboardData.distanceLabel.Text = string.format("%.1f studs", distance)
        billboardData.distanceLabel.TextColor3 = color
    end

    billboardData.nameLabel.Visible = name ~= nil
    billboardData.distanceLabel.Visible = distance ~= nil
end

function create2DBox(character, color, scale)
    local existing = character:FindFirstChild("ESP_2DBox")
    if existing then
        existing:Destroy()
    end

    local billboard = Instance.new("BillboardGui")
    billboard.Name = "ESP_2DBox"

    local rootPart = character:FindFirstChild("HumanoidRootPart")
    if rootPart then
        billboard.Adornee = rootPart
        billboard.Parent = rootPart
    else
        billboard.Adornee = character
        billboard.Parent = character
    end

    billboard.AlwaysOnTop = true
    billboard.Size = UDim2.new(0, 80 * scale, 0, 100 * scale)
    billboard.StudsOffset = Vector3.new(0, 0, 0)
    billboard.ClipsDescendants = false

    local boxFrame = Instance.new("Frame")
    boxFrame.Name = "BoxFrame"
    boxFrame.Size = UDim2.new(1, 0, 1, 0)
    boxFrame.BackgroundTransparency = 1
    boxFrame.BorderSizePixel = 0
    boxFrame.Parent = billboard

    local uiStroke = Instance.new("UIStroke")
    uiStroke.Thickness = math.max(1.5 * scale, 1)
    uiStroke.Transparency = 0
    uiStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    uiStroke.Color = color
    uiStroke.Parent = boxFrame

    return {
        billboard = billboard,
        boxFrame = boxFrame,
        stroke = uiStroke,
        scale = scale
    }
end

function update2DBox(boxData, color, scale)
    if boxData then
        if boxData.stroke then
            boxData.stroke.Color = color
        end
        if boxData.billboard then
            boxData.billboard.Size = UDim2.new(0, 80 * scale, 0, 100 * scale)
        end
        if boxData.stroke then
            boxData.stroke.Thickness = math.max(1.5 * scale, 1)
        end
        boxData.scale = scale
    end
end

function remove2DBox(character)
    local box = character:FindFirstChild("ESP_2DBox")
    if box then
        box:Destroy()
    end
    local rootPart = character:FindFirstChild("HumanoidRootPart")
    if rootPart then
        local boxInRoot = rootPart:FindFirstChild("ESP_2DBox")
        if boxInRoot then
            boxInRoot:Destroy()
        end
    end
end

function createHighlight(character, color)
    local existing = character:FindFirstChild("ESP_Highlight")
    if existing then
        existing:Destroy()
    end

    local highlight = Instance.new("Highlight")
    highlight.Name = "ESP_Highlight"
    highlight.Adornee = character
    highlight.FillColor = color
    highlight.OutlineColor = color
    highlight.FillTransparency = 0.5
    highlight.OutlineTransparency = 0.3
    highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    highlight.Parent = character

    return highlight
end

function updateHighlight(highlight, color)
    if highlight then
        highlight.FillColor = color
        highlight.OutlineColor = color
    end
end

function removeHighlight(character)
    local highlight = character:FindFirstChild("ESP_Highlight")
    if highlight then
        highlight:Destroy()
    end
end

function getGunColor()
    return Color3.fromRGB(255, 0, 255)
end

function findGunParts()
    local currentTime = tick()
    if currentTime - lastGunScanTime < gunUpdateInterval then
        return cachedGuns
    end
    
    local guns = {}
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("BasePart") and obj.Name == "GunDrop" and obj.Parent then
            table.insert(guns, obj)
        end
    end
    
    cachedGuns = guns
    lastGunScanTime = currentTime
    return guns
end

function cleanupGunESP()
    for gun, esp in pairs(gunEspElements) do
        if esp.box2D then
            local box = gun:FindFirstChild("ESP_2DBox")
            if box then box:Destroy() end
            local rootPart = gun:FindFirstChild("HumanoidRootPart")
            if rootPart then
                local boxInRoot = rootPart:FindFirstChild("ESP_2DBox")
                if boxInRoot then boxInRoot:Destroy() end
            end
        end
        if esp.box3D then remove3DBox(gun) end
        if esp.highlight then removeHighlight(gun) end
        if esp.billboard then
            local bill = gun:FindFirstChild("ESP_Billboard")
            if bill then bill:Destroy() end
            local rootPart = gun:FindFirstChild("HumanoidRootPart")
            if rootPart then
                local billInRoot = rootPart:FindFirstChild("ESP_Billboard")
                if billInRoot then billInRoot:Destroy() end
            end
        end
    end
    gunEspElements = {}
end

function cleanupRoleESP()
    for character, esp in pairs(roleEspElements) do
        if esp.box2D then
            local box = character:FindFirstChild("ESP_2DBox")
            if box then box:Destroy() end
            local rootPart = character:FindFirstChild("HumanoidRootPart")
            if rootPart then
                local boxInRoot = rootPart:FindFirstChild("ESP_2DBox")
                if boxInRoot then boxInRoot:Destroy() end
            end
        end
        if esp.box3D then remove3DBox(character) end
        if esp.highlight then removeHighlight(character) end
        if esp.billboard then
            local bill = character:FindFirstChild("ESP_Billboard")
            if bill then bill:Destroy() end
            local rootPart = character:FindFirstChild("HumanoidRootPart")
            if rootPart then
                local billInRoot = rootPart:FindFirstChild("ESP_Billboard")
                if billInRoot then billInRoot:Destroy() end
            end
        end
    end
    roleEspElements = {}
end

function removeDeadESPEntries()
    local toRemove = {}
    for gun, esp in pairs(gunEspElements) do
        if not gun or not gun.Parent then
            if esp.box2D then
                pcall(function() remove2DBox(gun) end)
            end
            if esp.box3D then
                pcall(function() remove3DBox(gun) end)
            end
            if esp.highlight then
                pcall(function() removeHighlight(gun) end)
            end
            if esp.billboard then
                pcall(function()
                    local bill = gun:FindFirstChild("ESP_Billboard")
                    if bill then bill:Destroy() end
                end)
            end
            table.insert(toRemove, gun)
        end
    end
    
    for _, gun in ipairs(toRemove) do
        gunEspElements[gun] = nil
    end
end

function updateGunESP()
    if not isRendering or not windowFocused then return end
    if not workspace.CurrentCamera then return end
    
    frameSkipCounter = frameSkipCounter + 1
    if frameSkipCounter % FRAME_SKIP ~= 0 then return end

    local currentTime = tick()
    if currentTime - lastGunUpdate < gunUpdateInterval then return end
    lastGunUpdate = currentTime

    removeDeadESPEntries()

    local currentTargets = {}
    local guns = findGunParts()

    for _, gun in ipairs(guns) do
        if gun and gun.Parent then
            currentTargets[gun] = true

            if not gunEspElements[gun] then
                gunEspElements[gun] = {}
            end

            local esp = gunEspElements[gun]
            local distance = getDistanceFromCamera(gun.Position)
            local scale = calculateBoxScale(distance)
            local gunColor = getGunColor()

            if gunBoxesEnabled then
                if gunBoxType == "2D" then
                    if not esp.box2D then
                        esp.box2D = create2DBox(gun, gunColor, scale)
                    end
                    if esp.box2D then
                        update2DBox(esp.box2D, gunColor, scale)
                    end
                    if esp.box3D then
                        remove3DBox(gun)
                        esp.box3D = nil
                    end
                else
                    if not esp.box3D then
                        esp.box3D = create3DBox(gun, gunColor, Vector3.new(3, 3, 3))
                    end
                    if esp.box3D then
                        update3DBoxColor(gun, gunColor)
                    end
                    if esp.box2D then
                        remove2DBox(gun)
                        esp.box2D = nil
                    end
                end
            else
                if esp.box2D then 
                    remove2DBox(gun)
                    esp.box2D = nil
                end
                if esp.box3D then 
                    remove3DBox(gun)
                    esp.box3D = nil
                end
            end

            if gunHighlightsEnabled then
                if not esp.highlight then
                    esp.highlight = createHighlight(gun, gunColor)
                end
                if esp.highlight then
                    updateHighlight(esp.highlight, gunColor)
                end
            else
                if esp.highlight then
                    removeHighlight(gun)
                    esp.highlight = nil
                end
            end

            if gunNamesEnabled or gunDistanceEnabled then
                if not esp.billboard then
                    esp.billboard = createBillboard(gun, "Gun", gunColor)
                end
                if esp.billboard then
                    local displayDistance = gunDistanceEnabled and distance or nil
                    updateBillboard(esp.billboard, gunNamesEnabled and "Gun" or nil, displayDistance, gunColor)
                end
            else
                if esp.billboard then
                    local bill = gun:FindFirstChild("ESP_Billboard")
                    if bill then bill:Destroy() end
                    local rootPart = gun:FindFirstChild("HumanoidRootPart")
                    if rootPart then
                        local billInRoot = rootPart:FindFirstChild("ESP_Billboard")
                        if billInRoot then billInRoot:Destroy() end
                    end
                    esp.billboard = nil
                end
            end
        end
    end

    local gunsToRemove = {}
    for gun, esp in pairs(gunEspElements) do
        if not currentTargets[gun] then
            if esp.box2D then 
                remove2DBox(gun)
            end
            if esp.box3D then 
                remove3DBox(gun)
            end
            if esp.highlight then 
                removeHighlight(gun)
            end
            if esp.billboard then
                local bill = gun:FindFirstChild("ESP_Billboard")
                if bill then bill:Destroy() end
                local rootPart = gun:FindFirstChild("HumanoidRootPart")
                if rootPart then
                    local billInRoot = rootPart:FindFirstChild("ESP_Billboard")
                    if billInRoot then billInRoot:Destroy() end
                end
            end
            table.insert(gunsToRemove, gun)
        end
    end
    
    for _, gun in ipairs(gunsToRemove) do
        gunEspElements[gun] = nil
    end
end

function updateRoleESP()
    if not isRendering or not windowFocused then return end
    if not workspace.CurrentCamera then return end
    
    local currentTime = tick()
    if currentTime - lastRoleUpdate < roleUpdateInterval then return end
    lastRoleUpdate = currentTime

    local currentTargets = {}

    for _, otherPlayer in ipairs(Players:GetPlayers()) do
        if otherPlayer ~= LocalPlayer then
            local character = otherPlayer.Character
            if character and character:FindFirstChild("HumanoidRootPart") then
                local humanoid = character:FindFirstChild("Humanoid")
                if humanoid and humanoid.Health > 0 then
                    local role, isDead = getPlayerRoleAndStatus(otherPlayer)
                    if role and RoleList[role] then
                        currentTargets[character] = true

                        if not roleEspElements[character] then
                            roleEspElements[character] = {}
                        end

                        local esp = roleEspElements[character]
                        local distance = getDistanceFromCamera(character.HumanoidRootPart.Position)
                        local scale = calculateBoxScale(distance)
                        local roleColor = getRoleColor(role, isDead)
                        local settings = roleSettings[role]

                        if settings and settings.boxesEnabled then
                            if settings.boxType == "2D" then
                                if not esp.box2D then
                                    esp.box2D = create2DBox(character, roleColor, scale)
                                end
                                if esp.box2D then
                                    update2DBox(esp.box2D, roleColor, scale)
                                end
                                if esp.box3D then
                                    remove3DBox(character)
                                    esp.box3D = nil
                                end
                            else
                                local boxSize = Vector3.new(4, 5, 3)
                                if humanoid then
                                    boxSize = Vector3.new(2, humanoid.HipHeight + 5, 2)
                                end
                                if not esp.box3D then
                                    esp.box3D = create3DBox(character, roleColor, boxSize)
                                end
                                if esp.box3D then
                                    update3DBoxColor(character, roleColor)
                                end
                                if esp.box2D then
                                    remove2DBox(character)
                                    esp.box2D = nil
                                end
                            end
                        else
                            if esp.box2D then 
                                remove2DBox(character)
                                esp.box2D = nil
                            end
                            if esp.box3D then 
                                remove3DBox(character)
                                esp.box3D = nil
                            end
                        end

                        if settings and settings.highlightsEnabled then
                            if not esp.highlight then
                                esp.highlight = createHighlight(character, roleColor)
                            end
                            if esp.highlight then
                                updateHighlight(esp.highlight, roleColor)
                            end
                        else
                            if esp.highlight then
                                removeHighlight(character)
                                esp.highlight = nil
                            end
                        end

                        if settings and (settings.namesEnabled or settings.distanceEnabled) then
                            if not esp.billboard then
                                esp.billboard = createBillboard(character, otherPlayer.Name, roleColor)
                            end
                            if esp.billboard then
                                local displayDistance = settings.distanceEnabled and distance or nil
                                updateBillboard(esp.billboard, settings.namesEnabled and otherPlayer.Name or nil, displayDistance, roleColor)
                            end
                        else
                            if esp.billboard then
                                local bill = character:FindFirstChild("ESP_Billboard")
                                if bill then bill:Destroy() end
                                local rootPart = character:FindFirstChild("HumanoidRootPart")
                                if rootPart then
                                    local billInRoot = rootPart:FindFirstChild("ESP_Billboard")
                                    if billInRoot then billInRoot:Destroy() end
                                end
                                esp.billboard = nil
                            end
                        end
                    end
                end
            end
        end
    end

    local charsToRemove = {}
    for character, esp in pairs(roleEspElements) do
        if not currentTargets[character] then
            if esp.box2D then 
                remove2DBox(character)
            end
            if esp.box3D then 
                remove3DBox(character)
            end
            if esp.highlight then 
                removeHighlight(character)
            end
            if esp.billboard then
                local bill = character:FindFirstChild("ESP_Billboard")
                if bill then bill:Destroy() end
                local rootPart = character:FindFirstChild("HumanoidRootPart")
                if rootPart then
                    local billInRoot = rootPart:FindFirstChild("ESP_Billboard")
                    if billInRoot then billInRoot:Destroy() end
                end
            end
            table.insert(charsToRemove, character)
        end
    end
    
    for _, character in ipairs(charsToRemove) do
        roleEspElements[character] = nil
    end
end

function isESPNeded()
    if gunBoxesEnabled or gunNamesEnabled or gunDistanceEnabled or gunHighlightsEnabled then
        return true
    end
    
    for roleName, settings in pairs(roleSettings) do
        if settings.boxesEnabled or settings.namesEnabled or settings.distanceEnabled or settings.highlightsEnabled then
            return true
        end
    end
    
    return false
end

function createRoleSections()
    for roleName, roleColor in pairs(RoleList) do
        local hexColor = getRoleHexColor(roleName)
        local richTitle = string.format('<font color="%s">%s ESP</font>', hexColor, roleName)

        Tabs.ESP:AddSpace({ Height = 20 })
        local CollapsedSection = Tabs.ESP:AddCollapsibleSection(richTitle, "solar/widget-2-bold", false)

        CollapsedSection:AddToggle(roleName .. "Boxes", {
            Title = roleName .. " Boxes",
            Default = false,
            Callback = function(DConfiguration)
                if roleSettings[roleName] then
                    roleSettings[roleName].boxesEnabled = DConfiguration
                end
                checkAndUpdateRenderLoop()
            end
        })

        CollapsedSection:AddDropdown(roleName .. "BoxType", {
            Search = false,
            Title = roleName .. " Box Type",
            Values = {"2D", "3D"},
            Default = "2D",
            Callback = function(value)
                if roleSettings[roleName] then
                    roleSettings[roleName].boxType = value
                end
            end
        })

        CollapsedSection:AddToggle(roleName .. "Names", {
            Title = roleName .. " Names",
            Default = false,
            Callback = function(DConfiguration)
                if roleSettings[roleName] then
                    roleSettings[roleName].namesEnabled = DConfiguration
                end
                checkAndUpdateRenderLoop()
            end
        })

        CollapsedSection:AddToggle(roleName .. "Distance", {
            Title = roleName .. " Distance",
            Default = false,
            Callback = function(DConfiguration)
                if roleSettings[roleName] then
                    roleSettings[roleName].distanceEnabled = DConfiguration
                end
                checkAndUpdateRenderLoop()
            end
        })

        CollapsedSection:AddSpace({ Height = 20 })
        CollapsedSection:AddToggle(roleName .. "Highlights", {
            Title = roleName .. " Highlights",
            Default = false,
            Callback = function(DConfiguration)
                if roleSettings[roleName] then
                    roleSettings[roleName].highlightsEnabled = DConfiguration
                end
                checkAndUpdateRenderLoop()
            end
        })
    end
end

renderConnection = nil
lastRenderTime = tick()
renderCheckConnection = nil

function onRenderStepped()
    lastRenderTime = tick()
    isRendering = true

    if gunBoxesEnabled or gunNamesEnabled or gunDistanceEnabled or gunHighlightsEnabled then
        updateGunESP()
    else
        if next(gunEspElements) ~= nil then
            cleanupGunESP()
        end
    end

    local anyRoleActive = false
    for roleName, settings in pairs(roleSettings) do
        if settings.boxesEnabled or settings.namesEnabled or settings.distanceEnabled or settings.highlightsEnabled then
            anyRoleActive = true
            break
        end
    end

    if anyRoleActive then
        updateRoleESP()
    else
        if next(roleEspElements) ~= nil then
            cleanupRoleESP()
        end
    end
    
    if not isESPNeded() then
        stopRenderLoop()
    end
end

function startRenderLoop()
    if renderConnection then return end
    if not isESPNeded() then return end
    renderConnection = RunService.RenderStepped:Connect(onRenderStepped)
end

function stopRenderLoop()
    if renderConnection then
        renderConnection:Disconnect()
        renderConnection = nil
    end
end

function checkAndUpdateRenderLoop()
    if isESPNeded() then
        startRenderLoop()
    else
        cleanupAllESP()
        stopRenderLoop()
    end
end

function cleanupAllESP()
    cleanupGunESP()
    cleanupRoleESP()
end

createRoleSections()

renderCheckConnection = RunService.Heartbeat:Connect(function()
    local currentTime = tick()
    if currentTime - lastRenderTime > 1 then
        isRendering = false
        cleanupAllESP()
    end
end)

UserInputService.WindowFocusReleased:Connect(function()
    windowFocused = false
    isRendering = false
    cleanupAllESP()
end)

UserInputService.WindowFocused:Connect(function()
    windowFocused = true
    isRendering = true
    checkAndUpdateRenderLoop()
end)

game:GetService("GuiService"):GetPropertyChangedSignal("MenuIsOpen"):Connect(function()
    if game:GetService("GuiService").MenuIsOpen then
        isRendering = false
        cleanupAllESP()
    else
        isRendering = true
        checkAndUpdateRenderLoop()
    end
end)

Players.PlayerRemoving:Connect(function(leavingPlayer)
    if leavingPlayer == LocalPlayer then
        cleanupAllESP()
        stopRenderLoop()
    end
end)

GunESPSection = Tabs.ESP:AddSection("Gun ESP", "solar/widget-2-bold")

GunESPSection:AddToggle("GunBoxes", {
    Title = "Gun Boxes",
    Default = false,
    Callback = function(DConfiguration) 
        gunBoxesEnabled = DConfiguration
        checkAndUpdateRenderLoop()
    end
})

GunESPSection:AddDropdown("GunBoxType", {
    Search = false,
    Title = "Gun Box Type",
    Values = {"2D", "3D"},
    Default = "2D",
    Callback = function(value) gunBoxType = value end
})

GunESPSection:AddToggle("GunNames", {
    Title = "Gun Names",
    Default = false,
    Callback = function(DConfiguration) 
        gunNamesEnabled = DConfiguration
        checkAndUpdateRenderLoop()
    end
})

GunESPSection:AddToggle("GunDistance", {
    Title = "Gun Distance",
    Default = false,
    Callback = function(DConfiguration) 
        gunDistanceEnabled = DConfiguration
        checkAndUpdateRenderLoop()
    end
})

GunESPSection:AddSpace({ Height = 20 })
GunESPSection:AddToggle("GunHighlights", {
    Title = "Gun Highlights",
    Default = false,
    Callback = function(DConfiguration) 
        gunHighlightsEnabled = DConfiguration
        checkAndUpdateRenderLoop()
    end
})

secTeleportAuto1 = Tabs.Teleport:AddSection("Teleport", "solar/widget-2-bold")
secTeleportAuto1:AddDivider()

function GetPlayerList()
    local playerList = {}
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer then
            table.insert(playerList, plr.DisplayName .. " (@" .. plr.Name .. ")")
        end
    end
    if #playerList == 0 then
        table.insert(playerList, "No players found")
    end
    return playerList
end

TeleportPlayerDropdown = secTeleportAuto1:AddDropdown("TeleportPlayerDropdown", {
    DropdownOutsideWindow = true,
    Title = "Select Player",
    Values = GetPlayerList(),
    Default = 1,
    Callback = function(value)
        selectedPlayerOption = value
    end
})

function UpdatePlayerList()
    TeleportPlayerDropdown:SetValues(GetPlayerList())
    TeleportPlayerDropdown:SetValue("Select a player")
end

function TeleportToPlayer(x, y, z)
    if selectedPlayerOption and selectedPlayerOption.Title ~= "No players found" then
        local targetPlayer = nil
        for _, plr in ipairs(Players:GetPlayers()) do
            if plr.DisplayName == selectedPlayerOption.Title or plr.Name == selectedPlayerOption.Desc:sub(2) then
                targetPlayer = plr
                break
            end
        end

        if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                LocalPlayer.Character.HumanoidRootPart.CFrame = targetPlayer.Character.HumanoidRootPart.CFrame * CFrame.new(x or 0, y or 0, z or 0)
            end
        end
    end
end

function TeleportToRandomPlayer(x, y, z)
    local otherPlayers = {}
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
            table.insert(otherPlayers, plr)
        end
    end

    if #otherPlayers > 0 then
        local randomPlayer = otherPlayers[math.random(1, #otherPlayers)]
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
            LocalPlayer.Character.HumanoidRootPart.CFrame = randomPlayer.Character.HumanoidRootPart.CFrame * CFrame.new(x or 0, y or 0, z or 0)
        end
    end
end

function TeleportToCoin(x, y, z)
    local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

    local coinServer = workspace:FindFirstChild("Coin_Server")
    if not coinServer then
        return
    end

    local coins = {}
    for _, coin in ipairs(coinServer:GetChildren()) do
        if coin:IsA("BasePart") then
            table.insert(coins, coin)
        end
    end

    if #coins == 0 then
        return
    end

    local targetCoin = coins[math.random(1, #coins)]
    local targetPos = targetCoin.Position + Vector3.new(x or 0, y or 5, z or 0)

    humanoidRootPart.CFrame = CFrame.new(targetPos)
end

function TeleportToMap(x, y, z)
    local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

    local spawnParts = {}
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("BasePart") and obj.Name == "Spawn" then
            local isInLobby = false
            local parent = obj.Parent
            while parent ~= nil do
                if (parent.Name == "Lobby" or parent.Name == "RegularLobby") and parent.Parent == workspace then
                    isInLobby = true
                    break
                end
                parent = parent.Parent
            end

            if not isInLobby then
                table.insert(spawnParts, obj)
            end
        end
    end

    if #spawnParts == 0 then
        return
    end

    local randomIndex = math.random(1, #spawnParts)
    local randomSpawn = spawnParts[randomIndex]

    humanoidRootPart.CFrame = randomSpawn.CFrame * CFrame.new(x or 0, y or 5, z or 0)
end

function TeleportToLobby(x, y, z)
    local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

    local lobby = workspace:FindFirstChild("Lobby") or workspace:FindFirstChild("RegularLobby")
    if not lobby then
        return
    end

    local spawns = lobby:FindFirstChild("Spawns")
    if not spawns then
        return
    end

    local spawnLocations = {}
    for _, obj in pairs(spawns:GetChildren()) do
        if obj:IsA("SpawnLocation") then
            table.insert(spawnLocations, obj)
        end
    end

    if #spawnLocations == 0 then
        return
    end

    local randomIndex = math.random(1, #spawnLocations)
    local randomSpawn = spawnLocations[randomIndex]

    humanoidRootPart.CFrame = randomSpawn.CFrame * CFrame.new(x or 0, y or 3, z or 0)
end

function TeleportToInnocent(x, y, z)
    local murderer = GetMurderer()
    local sheriff = GetSheriff()

    local innocents = {}
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer and plr ~= murderer and plr ~= sheriff and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
            local hasKnife = plr.Backpack:FindFirstChild("Knife") or (plr.Character and plr.Character:FindFirstChild("Knife"))
            local hasGun = plr.Backpack:FindFirstChild("Gun") or (plr.Character and plr.Character:FindFirstChild("Gun"))
            if not hasKnife and not hasGun then
                table.insert(innocents, plr)
            end
        end
    end

    if #innocents > 0 then
        local randomInnocent = innocents[math.random(1, #innocents)]
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
            LocalPlayer.Character.HumanoidRootPart.CFrame = randomInnocent.Character.HumanoidRootPart.CFrame * CFrame.new(x or 0, y or 0, z or 0)
        end
    end
end

function TeleportToMurderer(x, y, z)
    local murderer = GetMurderer()
    if murderer and murderer.Character and murderer.Character:FindFirstChild("HumanoidRootPart") then
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
            LocalPlayer.Character.HumanoidRootPart.CFrame = murderer.Character.HumanoidRootPart.CFrame * CFrame.new(x or 0, y or 0, z or 0)
        end
    end
end

function TeleportToSheriff(x, y, z)
    local sheriff = GetSheriff()
    if sheriff and sheriff.Character and sheriff.Character:FindFirstChild("HumanoidRootPart") then
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
            LocalPlayer.Character.HumanoidRootPart.CFrame = sheriff.Character.HumanoidRootPart.CFrame * CFrame.new(x or 0, y or 0, z or 0)
        end
    end
end

function TeleportToSecurityPart(x, y, z)
    local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

    local securityParts = {}
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj.Name == "SecurityPart" and obj:IsA("BasePart") then
            table.insert(securityParts, obj)
        end
    end

    if #securityParts == 0 then
        return
    end

    local randomIndex = math.random(1, #securityParts)
    local targetPart = securityParts[randomIndex]

    humanoidRootPart.CFrame = targetPart.CFrame * CFrame.new(x or 0, y or 3, z or 0)
end


secTeleportAuto1:AddButton({
    Title = "Teleport to Player",
    Description = "Teleport to the selected player",
    Icon = "user",
    Callback = function()
        TeleportToPlayer(0, 0, 0)
    end
})


secTeleportAuto1:AddButton({
    Title = "Teleport to Random Player",
    Description = "Teleport to a random player in the server",
    Icon = "users",
    Callback = function()
        TeleportToRandomPlayer(0, 0, 0)
    end
})


secTeleportAuto1:AddSpace({ Height = 20 })
secTeleportAuto1:AddButton({
    Title = "Teleport to Innocent",
    Description = "Teleport to a random innocent player",
    Icon = "user",
    Callback = function()
        TeleportToInnocent(0, 0, 0)
    end
})


secTeleportAuto1:AddButton({
    Title = "Teleport to Murderer",
    Icon = "user-x",
    Callback = function()
        TeleportToMurderer(0, 0, 0)
    end
})


secTeleportAuto1:AddButton({
    Title = "Teleport to Sheriff",
    Icon = "user-check",
    Callback = function()
        TeleportToSheriff(0, 0, 0)
    end
})


secTeleportAuto1:AddButton({
    Title = "Teleport to Dropped Gun",
    Icon = "target",
    Callback = function()
        GunTP()
    end
})


secTeleportAuto1:AddButton({
    Title = "Teleport to Coin",
    Icon = "dollar-sign",
    Callback = function()
        TeleportToCoin(0, 5, 0)
    end
})


secTeleportAuto1:AddSpace({ Height = 20 })
secTeleportAuto1:AddButton({
    Title = "Teleport to Map",
    Icon = "map",
    Callback = function()
        TeleportToMap(0, 5, 0)
    end
})


secTeleportAuto1:AddButton({
    Title = "Teleport Above Map",
    Description = "Teleport high above a random map spawn",
    Icon = "arrow-up",
    Callback = function()
        TeleportToMap(0, 950, 0)
    end
})


secTeleportAuto1:AddButton({
    Title = "Teleport to Lobby",
    Icon = "home",
    Callback = function()
        TeleportToLobby(0, 3, 0)
    end
})


secTeleportAuto1:AddButton({
    Title = "Teleport to SecurityPart",
    Description = "Teleport to Safe Spot",
    Icon = "shield",
    Callback = function()
        TeleportToSecurityPart(0, 3, 0)
    end
})

Players.PlayerAdded:Connect(function()
    UpdatePlayerList()
end)

Players.PlayerRemoving:Connect(function()
    UpdatePlayerList()
end)

LocalPlayer.CharacterAdded:Connect(function(newCharacter)
    setupCharacter(newCharacter)
end)


secMiscAuto1 = Tabs.Main:AddSection("Misc", "solar/widget-2-bold")
secMiscAuto1:AddDivider()

AntiAFKConnection = nil

startAntiAFK = function()
    AntiAFKConnection = LocalPlayer.Idled:Connect(function()
        VirtualUser:Button2Down(Vector2.new(0,0), workspace.CurrentCamera.CFrame)
        task.wait(1)
        VirtualUser:Button2Up(Vector2.new(0,0), workspace.CurrentCamera.CFrame)
    end)
end

stopAntiAFK = function()
    if AntiAFKConnection then
        AntiAFKConnection:Disconnect()
        AntiAFKConnection = nil
    end
end

AntiAFKToggle = secMiscAuto1:AddToggle("AntiAFKToggle", {
    Title = "Anti AFK",
    Default = AntiAFK,
    Callback = function(DConfiguration)
        if DConfiguration then
            startAntiAFK()
        else
            stopAntiAFK()
        end
    end
})

Tabs.Misc:AddSpace({ Height = 20 })
secMiscAuto2 = Tabs.Main:AddSection("Auto Glitch Vote Map", "solar/widget-2-bold")
secMiscAuto2:AddDivider()

AutoVote = {
    Enabled = false,
    MapType = 1,
    Connection = nil
}

function findVotePadContainer()
    local possibleLocations = {
        workspace:FindFirstChild("Lobby"),
        workspace:FindFirstChild("MapVote"),
        workspace:FindFirstChild("VotePad"),
        workspace
    }
    for _, location in pairs(possibleLocations) do
        if location then
            local mapVote = location:FindFirstChild("MapVote")
            if mapVote then
                return mapVote
            end
            local testPad = location:FindFirstChild("VotePad1")
            if testPad then
                return location
            end
        end
    end
    for _, child in pairs(workspace:GetChildren()) do
        local votePad = child:FindFirstChild("VotePad1")
        if votePad then
            return child
        end
    end
    return nil
end

function findAvailableVotePad()
    local votePadContainer = findVotePadContainer()
    if not votePadContainer then
        return nil, nil
    end
    local targetPad = votePadContainer:FindFirstChild("VotePad" .. AutoVote.MapType)
    if targetPad then
        return targetPad, AutoVote.MapType, votePadContainer
    end
    for i = 1, 10 do
        local pad = votePadContainer:FindFirstChild("VotePad" .. i)
        if pad then
            AutoVote.MapType = i
            return pad, i, votePadContainer
        end
    end
    return nil, nil, votePadContainer
end

function killYourself()
    local character = LocalPlayer.Character
    if character then
        local humanoid = character:FindFirstChild("Humanoid")
        if humanoid then
            humanoid.Health = 0
        end
    end
end

function teleportToVotePad()
    if not AutoVote.Enabled then
        return
    end
    local votePadModel, currentMapType = findAvailableVotePad()
    if not votePadModel then
        return
    end
    local mapInfoGui = votePadModel:FindFirstChild("MapInfoGui")
    if not mapInfoGui then
        return
    end
    local mapIcon = mapInfoGui:FindFirstChild("MapIcon")
    if not mapIcon or not mapIcon:IsA("ImageLabel") then
        return
    end
    local imageId = mapIcon.Image
    if imageId == "" or imageId == "rbxasset://textures/UI/ImagePlaceholder.png" then
        return
    end
    local primaryPart = votePadModel.PrimaryPart
    if not primaryPart then
        primaryPart = votePadModel:FindFirstChildWhichIsA("BasePart")
    end
    if primaryPart then
        local character = LocalPlayer.Character
        if character and character:FindFirstChild("HumanoidRootPart") then
            local humanoidRootPart = character.HumanoidRootPart
            local teleportPosition = primaryPart.Position + Vector3.new(0, 3, 0)
            humanoidRootPart.CFrame = CFrame.new(teleportPosition)
            task.wait(0.4)
            killYourself()
        end
    end
end

function voteLoop()
    while AutoVote.Enabled do
        teleportToVotePad()
        task.wait()
    end
end

secMiscAuto2:AddToggle("VoteGlitch", {
    Title = "Auto Vote Teleport",
    Default = AutoVote.Enabled,
    Callback = function(DConfiguration)
        AutoVote.Enabled = DConfiguration
        if DConfiguration then
            if AutoVote.Connection then
                task.cancel(AutoVote.Connection)
            end
            AutoVote.Connection = task.spawn(voteLoop)
        else
            if AutoVote.Connection then
                task.cancel(AutoVote.Connection)
            end
            AutoVote.Connection = nil
        end
    end
})

secMiscAuto2:AddDropdown("VoteMapGlitchType", {
    Search = false,
    Title = "Map Selection",
    Values = {"Map 1", "Map 2", "Map 3"},
    Default = "Map 1",
    Callback = function(mode)
        local mapNumber = tonumber(mode:match("%d+"))
        if mapNumber then
            AutoVote.MapType = mapNumber
            local votePadContainer = findVotePadContainer()
            if votePadContainer then
                local pad = votePadContainer:FindFirstChild("VotePad" .. AutoVote.MapType)
                if not pad then
                    for i = 1, 10 do
                        pad = votePadContainer:FindFirstChild("VotePad" .. i)
                        if pad then
                            AutoVote.MapType = i
                            break
                        end
                    end
                end
            end
        end
    end
})

LocalPlayer.CharacterAdded:Connect(function(character)
    character:WaitForChild("HumanoidRootPart")
    character:WaitForChild("Humanoid")
    if AutoVote.Enabled then
        teleportToVotePad()
    end
end)

if AutoVote.Enabled then
    AutoVote.Connection = task.spawn(voteLoop)
end

if not workspace:FindFirstChild("SecurityPart") then
    local SecurityPart = Instance.new("Part")
    SecurityPart.Name = "SecurityPart"
    SecurityPart.Size = Vector3.new(1000, 1, 1000)
    SecurityPart.Position = Vector3.new(50000, 50000, 50000)
    SecurityPart.Anchored = true
    SecurityPart.CanCollide = true
    SecurityPart.Parent = workspace
end

function startExpFarm()
    local securityPart = workspace:FindFirstChild("SecurityPart")
    if not securityPart then
        print("SecurityPart not found")
        return
    end
    ExpFarmConnection = RunService.Heartbeat:Connect(function()
        local character = LocalPlayer.Character
        local rootPart = character and character:FindFirstChild("HumanoidRootPart")
        if character and rootPart then
            rootPart.CFrame = securityPart.CFrame + Vector3.new(0, 3, 0)
        end
    end)
end

function stopExpFarm()
    if ExpFarmConnection then
        ExpFarmConnection:Disconnect()
        ExpFarmConnection = nil
    end
end

CollectionService = game:GetService("CollectionService")
TweenService = game:GetService("TweenService")

COIN_TAG = "Coin_Server"
for _, coin in ipairs(workspace:GetDescendants()) do
    if coin.Name == "Coin_Server" and coin:IsA("BasePart") then
        CollectionService:AddTag(coin, COIN_TAG)
    end
end

coinsCache = CollectionService:GetTagged(COIN_TAG)

AutoFarm = {
    Enabled = false,
    CoinCollectType = "Nearby",
    FullBagAction = "Reset",
    TweenSpeed = 20,
    TeleportDelay = 2,
    UndergroundFarm = false,
    AutoFarmType = "Tween"
}

currentCoins = 0
maxCoins = 0
lastCacheUpdate = 0
CACHE_UPDATE_INTERVAL = 0.5
coinCache = {}
moving = false
currentTarget = nil
currentTween = nil
lastTeleportTime = 0
isLaying = false
layConnection = nil
originalPlatformStand = false
autoFarmLoopConnection = nil
endRoundShootConnection = nil
endRoundShootEnabled = false
safeSpotPosition = nil

function GetSafeSpot()
    if not safeSpotPosition then
        local securityPart = workspace:FindFirstChild("SecurityPart")
        if securityPart then
            safeSpotPosition = securityPart.CFrame + Vector3.new(0, 3, 0)
        else
            safeSpotPosition = CFrame.new(50000, 50000, 50000)
        end
    end
    return safeSpotPosition
end

function TeleportToSafeSpot()
    local character = LocalPlayer.Character
    local rootPart = character and character:FindFirstChild("HumanoidRootPart")
    if rootPart then
        rootPart.CFrame = GetSafeSpot()
    end
end

function toggleLay(DConfiguration)
    isLaying = DConfiguration
    if DConfiguration and not IsPlayerDead() then
        originalPlatformStand = Humanoid.PlatformStand
        Humanoid.Sit = true
        Humanoid.PlatformStand = true
        local currentPos = HumanoidRootPart.Position
        HumanoidRootPart.CFrame = CFrame.new(currentPos) * CFrame.Angles(math.pi * 0.5, 0, 0)
        for _, anim in ipairs(Humanoid:GetPlayingAnimationTracks()) do
            anim:Stop()
        end
        if layConnection then layConnection:Disconnect() end
        layConnection = RunService.Heartbeat:Connect(function()
            if isLaying and HumanoidRootPart then
                local currentPos = HumanoidRootPart.Position
                HumanoidRootPart.CFrame = CFrame.new(currentPos) * CFrame.Angles(math.pi * 0.5, 0, 0)
            end
        end)
    else
        if layConnection then
            layConnection:Disconnect()
            layConnection = nil
        end
        Humanoid.Sit = false
        Humanoid.PlatformStand = originalPlatformStand
        local currentPos = HumanoidRootPart.Position
        HumanoidRootPart.CFrame = CFrame.new(currentPos)
    end
end

function isCoinCollected(coin)
    return coin:GetAttribute("Collected") == true
end

function isFullCoinBag()
    return currentCoins >= maxCoins and maxCoins > 0
end

function ResetCoinBag()
    currentCoins = 0
    maxCoins = 0
end

function stopCurrentTween()
    if currentTween then
        currentTween:Cancel()
        currentTween = nil
    end
    if HumanoidRootPart then
        HumanoidRootPart.Anchored = false
    end
    moving = false
    currentTarget = nil
end

function TeleportToLobby()
    local lobby = workspace:FindFirstChild("Lobby") or workspace:FindFirstChild("RegularLobby")
    if not lobby then return end
    local spawns = lobby:FindFirstChild("Spawns")
    if not spawns then return end
    local spawnLocations = {}
    for _, obj in pairs(spawns:GetChildren()) do
        if obj:IsA("SpawnLocation") then
            table.insert(spawnLocations, obj)
        end
    end
    if #spawnLocations == 0 then return end
    local randomSpawn = spawnLocations[math.random(1, #spawnLocations)]
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        LocalPlayer.Character.HumanoidRootPart.CFrame = randomSpawn.CFrame + Vector3.new(0, 3, 0)
    end
end

function startEndRoundShoot()
    if endRoundShootConnection then endRoundShootConnection:Disconnect() end
    endRoundShootEnabled = true
    endRoundShootConnection = RunService.Heartbeat:Connect(function()
        if endRoundShootEnabled then
            if IsPlayerDead() then
                stopEndRoundShoot()
                ResetCoinBag()
                return
            end
            local murderer = GetMurderer()
            if murderer and murderer.Character and murderer.Character:FindFirstChild("Humanoid") and murderer.Character.Humanoid.Health > 0 then
                TeleportToSafeSpot()
                task.wait(0.1)
                ShootMurderer()
            end
            task.wait(2)
        end
    end)
end

function stopEndRoundShoot()
    endRoundShootEnabled = false
    if endRoundShootConnection then
        endRoundShootConnection:Disconnect()
        endRoundShootConnection = nil
    end
end

function handleFullBag()
    if AutoFarm.FullBagAction == "Reset" then
        if isLaying then
            toggleLay(false)
        end
        if Humanoid and Humanoid.Health > 0 then
            Humanoid.Health = 0
        end
    elseif AutoFarm.FullBagAction == "Teleport to lobby" then
        stopCurrentTween()
        if isLaying then
            toggleLay(false)
        end
        TeleportToLobby()
    elseif AutoFarm.FullBagAction == "End Round" then
        if isLaying then
            toggleLay(false)
        end
        local playerRole = nil
        if GetPlayerRole then
            playerRole = GetPlayerRole(LocalPlayer.Name)
        elseif LocalPlayer:FindFirstChild("Role") then
            playerRole = LocalPlayer.Role.Value
        elseif LocalPlayer:FindFirstChild("PlayerRole") then
            playerRole = LocalPlayer.PlayerRole.Value
        end
        if playerRole == "Innocent" then
            FlingRole("Murderer")
        elseif playerRole == "Hero" or playerRole == "Sheriff" then
            MagicBulletEnabled = true
            startEndRoundShoot()
        elseif playerRole == "Murderer" or playerRole == "Assassin" then
            if KnifeCombat and KnifeCombat.killAll then
                KnifeCombat.killAll()
            end
        end
    end
end

workspace.DescendantAdded:Connect(function(descendant)
    if descendant.Name == "Coin_Server" and descendant:IsA("BasePart") then
        CollectionService:AddTag(descendant, COIN_TAG)
        if not isCoinCollected(descendant) then
            table.insert(coinsCache, descendant)
        end
    end
end)

workspace.DescendantRemoving:Connect(function(descendant)
    if descendant.Name == "Coin_Server" and descendant:IsA("BasePart") then
        CollectionService:RemoveTag(descendant, COIN_TAG)
        local index = table.find(coinsCache, descendant)
        if index then
            table.remove(coinsCache, index)
        end
        if currentTarget == descendant then
            currentTarget = nil
        end
    end
end)

attributeChangedConnections = {}
function monitorCoinAttributes(coin)
    local conn = coin.AttributeChanged:Connect(function(attributeName)
        if attributeName == "Collected" and coin:GetAttribute("Collected") == true then
            local index = table.find(coinsCache, coin)
            if index then
                table.remove(coinsCache, index)
            end
            if currentTarget == coin then
                currentTarget = nil
            end
        end
    end)
    attributeChangedConnections[coin] = conn
end

for _, coin in ipairs(coinsCache) do
    monitorCoinAttributes(coin)
end

remote = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("Gameplay"):WaitForChild("CoinCollected")

remote.OnClientEvent:Connect(function(coinType, currentCoin, maxCoin, data)
    currentCoins = currentCoin    maxCoins = maxCoin
    if AutoFarm.Enabled and currentCoins >= maxCoins and maxCoins > 0 then
        stopCurrentTween()
        handleFullBag()
    end
end)

function updateCoinCacheList()
    coinCache = {}
    for _, coin in ipairs(coinsCache) do
        if coin and coin.Parent and not isCoinCollected(coin) then
            table.insert(coinCache, coin)
        end
    end
end

function findNearestCoinOptimized()
    updateCoinCacheList()
    if #coinCache == 0 then return nil end
    local nearest = nil
    local minDist = math.huge
    local rootPos = HumanoidRootPart.Position
    for _, coin in ipairs(coinCache) do
        local dist = (coin.Position - rootPos).Magnitude
        if dist < minDist then
            minDist = dist
            nearest = coin
        end
    end
    return nearest
end

function findRandomCoinOptimized()
    updateCoinCacheList()
    if #coinCache == 0 then return nil end
    return coinCache[math.random(1, #coinCache)]
end

function getTargetCoin()
    if AutoFarm.CoinCollectType == "Nearby" then
        return findNearestCoinOptimized()
    elseif AutoFarm.CoinCollectType == "Random" then
        return findRandomCoinOptimized()
    else
        return findNearestCoinOptimized()
    end
end

function teleportToTarget(target)
    if IsPlayerDead() then ResetCoinBag() return end
    if isFullCoinBag() then return end
    if not target or not target.Parent or isCoinCollected(target) then return end
    local currentTime = tick()
    if currentTime - lastTeleportTime < AutoFarm.TeleportDelay then
        task.wait(AutoFarm.TeleportDelay - (currentTime - lastTeleportTime))
    end
    local targetPos = target.Position + Vector3.new(0, 3, 0)
    if AutoFarm.UndergroundFarm then
        targetPos = target.Position + Vector3.new(0, -2, 0)
    end
    HumanoidRootPart.CFrame = CFrame.new(targetPos)
    if AutoFarm.UndergroundFarm and isLaying then
        HumanoidRootPart.CFrame = CFrame.new(targetPos) * CFrame.Angles(math.pi * 0.5, 0, 0)
    end
    lastTeleportTime = tick()
end

function tweenToTarget(target)
    if IsPlayerDead() then ResetCoinBag() return end
    if isFullCoinBag() then return end
    if not target or not target.Parent or isCoinCollected(target) then return end
    stopCurrentTween()
    if AutoFarm.UndergroundFarm and not isLaying then
        toggleLay(true)
    end
    HumanoidRootPart.Anchored = true
    local targetPos = target.Position + Vector3.new(0, 3, 0)
    if AutoFarm.UndergroundFarm then
        targetPos = target.Position + Vector3.new(0, -0.5, 0)
    end
    local targetCFrame
    if AutoFarm.UndergroundFarm and isLaying then
        targetCFrame = CFrame.new(targetPos) * CFrame.Angles(math.pi * 0.5, 0, 0)
    else
        targetCFrame = CFrame.new(targetPos)
    end
    local distance = (targetPos - HumanoidRootPart.Position).Magnitude
    local duration = distance / AutoFarm.TweenSpeed
    local tweenInfo = TweenInfo.new(duration, Enum.EasingStyle.Linear, Enum.EasingDirection.Out)
    local goal = {CFrame = targetCFrame}
    currentTween = TweenService:Create(HumanoidRootPart, tweenInfo, goal)
    currentTween.Completed:Connect(function()
        HumanoidRootPart.Anchored = false
        moving = false
        currentTarget = nil
        if AutoFarm.UndergroundFarm and isLaying then
            local currentPos = HumanoidRootPart.Position
            HumanoidRootPart.CFrame = CFrame.new(currentPos) * CFrame.Angles(math.pi * 0.5, 0, 0)
        end
    end)
    currentTween:Play()
    moving = true
    currentTarget = target
end

function startAutoFarmLoop()
    if autoFarmLoopConnection then return end
    autoFarmLoopConnection = RunService.Heartbeat:Connect(function()
        if IsPlayerDead() then ResetCoinBag() return end
        if isFullCoinBag() then return end
        if not AutoFarm.Enabled then
            stopCurrentTween()
            if isLaying then
                toggleLay(false)
            end
        elseif not moving then
            local target = getTargetCoin()
            if target and not isCoinCollected(target) then
                if AutoFarm.AutoFarmType == "Teleport" then
                    teleportToTarget(target)
                elseif AutoFarm.AutoFarmType == "Tween" then
                    tweenToTarget(target)
                end
            end
        end
    end)
end

function stopAutoFarmLoop()
    if autoFarmLoopConnection then
        autoFarmLoopConnection:Disconnect()
        autoFarmLoopConnection = nil
    end
    stopCurrentTween()
    if isLaying then
        toggleLay(false)
    end
end

LocalPlayer.CharacterAdded:Connect(function(newCharacter)
    setupCharacter(newCharacter)
    stopCurrentTween()
    stopEndRoundShoot()
    ResetCoinBag()
    if isLaying then
        toggleLay(false)
    end
    coinCache = {}
    lastCacheUpdate = 0
end)

connection = nil
Enabled = true

function GetCoinContainer()
    for _, v in pairs(workspace:GetChildren()) do
        local coinContainer = v:FindFirstChild("CoinContainer") or v:FindFirstChild("CoinsAreas")
        if coinContainer then
            return coinContainer
        end
    end
end

function CollectAllCoins()
    local CoinContainer = GetCoinContainer()
    if not CoinContainer then return end

    local character = LocalPlayer.Character
    if not character or not character:FindFirstChild("HumanoidRootPart") then return end

    local hrp = character.HumanoidRootPart

    for _, coin in pairs(CoinContainer:GetChildren()) do
        if coin.Name == "Coin_Server" and coin:FindFirstChildWhichIsA("TouchTransmitter") and coin:FindFirstChild("CoinVisual") then
            firetouchinterest(hrp, coin, 1)
            firetouchinterest(hrp, coin, 0)
        end
    end
end

function StartCoinAura()
    if connection then connection:Disconnect() end
    connection = RunService.Heartbeat:Connect(function()
        if Enabled then
            CollectAllCoins()
        end
    end)
end

function StopCoinAura()
    if connection then
        connection:Disconnect()
        connection = nil
    end
end

Tabs.Misc:AddSpace({ Height = 20 })
secMiscAuto3 = Tabs.Main:AddSection("Auto Farm", "solar/widget-2-bold")
secMiscAuto3:AddDivider()

autoFarmToggle = secMiscAuto3:AddToggle("AutoFarm", {
    Title = "Enable Auto Farm",
    Default = false,
    Callback = function(DConfiguration)
        AutoFarm.Enabled = DConfiguration
        if DConfiguration then
            AntiAFKToggle:SetValue(true)
            startAutoFarmLoop()
        else
            stopAutoFarmLoop()
            stopEndRoundShoot()
        end
    end
})

coinCollectTypeDropdown = secMiscAuto3:AddDropdown("coinCollectTypeDropdown", {
    Search = false,
    Title = "Coin Collect Type",
    Values = {"Nearby", "Random"},
    Default = "Nearby",
    Callback = function(value)
        AutoFarm.CoinCollectType = value
        currentTarget = nil
    end
})

autoFarmTypeDropdown = secMiscAuto3:AddDropdown("autoFarmTypeDropdown", {
    Search = false,
    Title = "Auto Farm Type",
    Values = {"Teleport", "Tween"},
    Default = "Tween",
    Callback = function(value)
        AutoFarm.AutoFarmType = value
        stopCurrentTween()
        if not AutoFarm.UndergroundFarm and isLaying then
            toggleLay(false)
        end
    end
})

secMiscAuto3:AddSpace({ Height = 20 })
fullBagActionDropdown = secMiscAuto3:AddDropdown("fullBagActionDropdown", {
    Search = false,
    Title = "Action Do when full bag",
    Values = {"Reset", "Teleport to lobby", "End Round"},
    Default = "Reset",
    Callback = function(value)
        AutoFarm.FullBagAction = value
    end
})

undergroundFarmToggle = secMiscAuto3:AddToggle("undergroundFarmToggle", {
    Title = "Underground Farm",
    Description = "Farm coins underground and lay down",
    Default = false,
    Type = "Checkbox",
    Callback = function(DConfiguration)
        AutoFarm.UndergroundFarm = DConfiguration
        if DConfiguration and AutoFarm.Enabled and AutoFarm.AutoFarmType == "Tween" then
            toggleLay(true)
        elseif not DConfiguration and isLaying then
            toggleLay(false)
        end
    end
})

tweenSpeedInput = secMiscAuto3:AddInput("tweenSpeedInput", {
    Title = "Farm Speed",
    Placeholder = "Speed for movement",
    Default = "20",
    Numeric = true,
    Callback = function(value)
        local num = tonumber(value)
        if num and num > 0 then
            AutoFarm.TweenSpeed = num
        end
    end
})

teleportDelayInput = secMiscAuto3:AddInput("teleportDelayInput", {
    Title = "Teleport Delay (seconds)",
    Placeholder = "Too low = kick",
    Default = "2",
    Numeric = true,
    Callback = function(value)
        local num = tonumber(value)
        if num and num >= 0 then
            AutoFarm.TeleportDelay = num
        end
    end
})

CoinAura = secMiscAuto3:AddToggle("CoinAura", {
    Title = "Coin Aura",
    Default = false,
    Callback = function(DConfiguration)
        if DConfiguration then
            StartCoinAura() 
        else
            StopCoinAura() 
        end
    end
})

secMiscAuto3:AddSpace({ Height = 20 })
ExpFarmToggle = secMiscAuto3:AddToggle("ExpFarmToggle", {
    Title = "Exp Farm",
    Default = false,
    Callback = function(DConfiguration)
        if DConfiguration then
            startExpFarm()
        else
            stopExpFarm()
        end
    end
})

function getPlayerRole(playerName)
    return GetPlayerRole(playerName)
end

Tabs.Misc:AddSpace({ Height = 20 })
secMiscAuto4 = Tabs.Main:AddSection("Role Revealer", "solar/widget-2-bold")
secMiscAuto4:AddDivider()

secMiscAuto4:AddButton({
    Title = "Reveal Murderer",
    Description = "Reveal murderer in chat",
    Icon = "user-x",
    Callback = function()
        local textchannels = game:GetService("TextChatService"):WaitForChild("TextChannels"):GetChildren()
        for _, textchannel in ipairs(textchannels) do
            if textchannel.Name == "RBXSystem" then continue end
            local murd = GetMurderer()
            if murd then
                local message = string.format("%s Is Murderer", murd.Name)
                textchannel:SendAsync(message)
            else
                local message = "No Murderer Found"
                textchannel:SendAsync(message)
            end
        end
    end
})

secMiscAuto4:AddButton({
    Title = "Reveal Sheriff/Hero",
    Description = "Reveal sheriff or hero in chat",
    Icon = "user-check",
    Callback = function()
        local textchannels = game:GetService("TextChatService"):WaitForChild("TextChannels"):GetChildren()
        for _, textchannel in ipairs(textchannels) do
            if textchannel.Name == "RBXSystem" then continue end
            local sher = GetSheriff()
            local hero = GetHero()
            if sher then 
                local message = string.format("%s Is Sheriff", sher.Name)
                textchannel:SendAsync(message)
            elseif hero then
                local message = string.format("%s Is Hero", hero.Name)
                textchannel:SendAsync(message)
            else
                local message = "No Sheriff/Hero Found"
                textchannel:SendAsync(message)
            end
        end
    end
})

secMiscAuto4:AddButton({
    Title = "Trade Helper",
    Compact = true,
    Callback = function()
        loadstring(game:HttpGet("https://pastebin.com/raw/8LDyigix"))()
        Fluent:Notify({ Title = "Trade Helper", Content = "Script loaded!", Duration = 3 })
    end
})

BombDelay = 20
isBombCooldown = false
bombButton = nil
cooldownConnection = nil
startTime = 0

function hasFakeBomb()
    local player = LocalPlayer
    if player.Character and player.Character:FindFirstChild("FakeBomb") then
        return true
    end
    if player.Backpack:FindFirstChild("FakeBomb") then
        return true
    end
    local toys = player.Backpack:FindFirstChild("Toys")
    if toys and toys:FindFirstChild("FakeBomb") then
        return true
    end
    return false
end

function DropFakeBomb()
    local player = LocalPlayer
    local backpack = player.Backpack
    local char = player.Character or player.CharacterAdded:Wait()
    local humanoid = char:FindFirstChildOfClass("Humanoid")
    local root = char:FindFirstChild("HumanoidRootPart")
    if not root or not humanoid then return end

    local bomb = backpack:FindFirstChild("FakeBomb") or char:FindFirstChild("FakeBomb")
    if not bomb then
        local remotes = ReplicatedStorage:FindFirstChild("Remotes")
        if remotes and remotes.Extras then
            remotes.Extras.ReplicateToy:InvokeServer("FakeBomb")
        end
        bomb = backpack:WaitForChild("FakeBomb", 2) or char:WaitForChild("FakeBomb", 2)
    end
    if not bomb then return end

    bomb.Parent = char
    if bomb:IsDescendantOf(char) and bomb.Remote then
        bomb.Remote:FireServer(root.CFrame * CFrame.new(0, -3, 0), 50)
        task.wait(0.05)
        humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
        local oldJump = humanoid.JumpPower
        humanoid.JumpPower = 53
        task.wait(0.3)
        bomb.Parent = backpack
        humanoid.JumpPower = oldJump
    end
end

function startCooldown()
    if cooldownConnection then
        cooldownConnection:Disconnect()
    end
    startTime = tick()
    if bombButton then
        bombButton:SetText("Bomb cooldown " .. BombDelay .. "s")
    end
    cooldownConnection = RunService.Stepped:Connect(function()
        if isBombCooldown then
            local elapsed = tick() - startTime
            local remaining = BombDelay - elapsed
            if remaining <= 0 then
                isBombCooldown = false
                if bombButton then
                    bombButton:SetText("Bomb MLG")
                end
                if cooldownConnection then
                    cooldownConnection:Disconnect()
                    cooldownConnection = nil
                end
            else
                if bombButton then
                    bombButton:SetText("Bomb cooldown " .. math.ceil(remaining) .. "s")
                end
            end
        elseif cooldownConnection then
            cooldownConnection:Disconnect()
            cooldownConnection = nil
        end
    end)
end

BombFrame, BombTextButton = FBModule:Create("BombMLG", "Bomb MLG", false, function(Btn)
    if isBombCooldown then return end
    if not hasFakeBomb() then
        FloatingButtonModule.SetText(Btn, "You Don't Have Fake Bomb")
        task.wait(3)
        FloatingButtonModule.SetText(Btn, "Bomb MLG")
        return
    end
    DropFakeBomb()
    isBombCooldown = true
    startCooldown()
end)
bombButton = {
    SetText = function(_, text) FloatingButtonModule.SetText(BombTextButton, text) end,
    SetVisible = function(_, DConfiguration) FloatingButtonModule.SetVisible(BombFrame, DConfiguration) end,
}

secMiscAuto4:AddSpace({ Height = 20 })
ShowBombMLGButtonToggle = secMiscAuto4:AddToggle("ShowBombMLGButtonToggle", {
    Title = "Show Bomb MLG Button",
    Default = false,
    Callback = function(DConfiguration)
        bombButton:SetVisible(DConfiguration)
    end,
})

FBModule:AddSizeInputs(secMiscAuto4, "BombMLG", "Bomb MLG")

FakeBombKeybind = secMiscAuto4:AddKeybind("FakeBombKeybind", {
    Title = "Bomb MLG Keybind",
    Default = "",
    Callback = function()
        if not hasFakeBomb() then
            Fluent:Notify({
                Title = "Fake Bomb",
                Content = "You don't have Fake Bomb",
                Duration = 3
            })
            return
        end
        if isBombCooldown then
            local remaining = BombDelay - (tick() - startTime)
            Fluent:Notify({
                Title = "Fake Bomb",
                Content = ("Bomb on cooldown " .. math.ceil(remaining) .. "s"),
                Duration = 3
            })
            return
        end
        DropFakeBomb()
        isBombCooldown = true
        startCooldown()
    end
})
secUtilityAntiAFK = Tabs.Utility:AddSection("Anti AFK", "solar/shield-check-bold")


disableInvisibleWallsEnabled = false
invisibleWallConnection = nil

function IsLikelyInvisibleWall(obj)
    return obj:IsA("BasePart") and obj.Transparency >= 0.95 and obj.CanCollide == true
end

function ApplyInvisibleWallState(obj, DConfiguration)
    if IsLikelyInvisibleWall(obj) then
        obj.CanCollide = not DConfiguration
    end
end

secUtilityAntiAFK:AddToggle("UtilityToggle138", {
    Title = "Disable Invisible Walls",
    Description = "Disables collision on all invisible walls",
    Default = false,
    Callback = function(DConfiguration)
        disableInvisibleWallsEnabled = DConfiguration

        for _, obj in pairs(workspace:GetDescendants()) do
            ApplyInvisibleWallState(obj, DConfiguration)
        end

        if invisibleWallConnection then
            invisibleWallConnection:Disconnect()
            invisibleWallConnection = nil
        end

        if DConfiguration then
            invisibleWallConnection = workspace.DescendantAdded:Connect(function(obj)
                task.wait()
                if disableInvisibleWallsEnabled then
                    ApplyInvisibleWallState(obj, true)
                end
            end)
        end
    end
})

secUtilityAntiAFK:AddButton({
    Title = "Unlock all emote",
    Callback = function()
        for i in pairs(require(game:GetService("ReplicatedStorage").Database.Sync).Emotes) do firesignal(game:GetService("ReplicatedStorage").Remotes.Inventory.ChangeInventoryItem.OnClientEvent,  "Emotes", i, 1) end
end
})

emoteInputValue = ""

secUtilityAntiAFK:AddInput("UtilityInput140", {
    Title = "Emote Name",
    Placeholder = "Enter emote name",
    Callback = function(emoteName)
        emoteInputValue = emoteName
    end
})

secUtilityAntiAFK:AddButton({
    Title = "Play Emote By Name",
    Callback = function()
        if emoteInputValue and emoteInputValue ~= "" then
            ReplicatedStorage.Remotes.Misc.PlayEmote:Fire(emoteInputValue)
        end
    end
})

hiddenfling = false
movel = 0.1
flingPower = 1e35
flingCoroutine = nil

function fling()
    local chr = LocalPlayer.Character
    if not chr then return end
    local hrp = chr:FindFirstChild("HumanoidRootPart")
    if not hrp then return end

    while hiddenfling and chr and hrp and hrp.Parent do
        local vel = hrp.Velocity
        hrp.Velocity = vel * flingPower + Vector3.new(0, flingPower, 0)
        RunService.RenderStepped:Wait()
        hrp.Velocity = vel
        RunService.Stepped:Wait()
        hrp.Velocity = vel + Vector3.new(0, movel, 0)
        movel = -movel
        RunService.Heartbeat:Wait()
    end
end

function startFling()
    if flingCoroutine then
        coroutine.close(flingCoroutine)
        flingCoroutine = nil
    end
    flingCoroutine = coroutine.create(fling)
    coroutine.resume(flingCoroutine)
end

function stopFling()
    if flingCoroutine then
        coroutine.close(flingCoroutine)
        flingCoroutine = nil
    end

    local chr = LocalPlayer.Character
    if chr then
        local hrp = chr:FindFirstChild("HumanoidRootPart")
        if hrp then
            hrp.Velocity = Vector3.new(0, 0, 0)
            hrp.RotVelocity = Vector3.new(0, 0, 0)
        end
    end
end


Tabs.Utility:AddSpace({ Height = 20 })
secUtilityFling = Tabs.Utility:AddSection("Fling", "solar/wind-bold")

TouchFlingToggle = secUtilityFling:AddToggle("TouchFlingToggle", {
    Title = "Touch Fling",
    Default = false,
    Callback = function(DConfiguration)
        hiddenfling = DConfiguration
        if DConfiguration then
            startFling()
        else
            stopFling()
        end
    end
})

LocalPlayer.CharacterAdded:Connect(function(character)
    if hiddenfling then
        task.wait(1)
        startFling()
    else
        task.wait(0.5)
        local hrp = character:FindFirstChild("HumanoidRootPart")
        if hrp then
            hrp.Velocity = Vector3.new(0, 0, 0)
            hrp.RotVelocity = Vector3.new(0, 0, 0)
        end
    end
end)

secUtilityFling:AddInput("FlingPower", {
    Title = "Fling Power",
    Placeholder = "Enter fling power (default: 1e35)",
    Callback = function(value)
        if value and value ~= "" then
            flingPower = tonumber(value) or 1e35
        end
    end
})

secUtilityFling:AddButton({
    Title = "Fling Tool",
    Icon = "rbxassetid://3836615692",
    Callback = function()
        local CharacterModel = LocalPlayer.Character
        local Humanoid = CharacterModel:WaitForChild("Humanoid")
        CharacterModel:WaitForChild("HumanoidRootPart")
        function FindPart(ParentModel, PartName, PartType)
            local FoundPart = nil
            pcall(function()
                local ParentModel = ParentModel
                local Iterator, Table, Key = pairs(ParentModel:GetChildren())
                while true do
                    local Value
                    Key, Value = Iterator(Table, Key)
                    if Key == nil then
                        break
                    end
                    if Value.Name == PartName and Value:IsA(PartType) then
                        FoundPart = Value
                        break
                    end
                end
            end)
            return FoundPart
        end
        local IsEnabled = false
        local RunService = game:GetService("RunService")
        local SteppedEvent = RunService.Stepped
        local HeartbeatEvent = RunService.Heartbeat
        local RenderSteppedEvent = RunService.RenderStepped
        local IsActive = true
        spawn(function()
            local Character = nil
            local Part = nil
            local VelocityMultiplier = 0.1
            while IsActive do
                HeartbeatEvent:Wait()
                if IsEnabled then
                    while IsEnabled and (IsActive and not (Character and (Character.Parent and (Part and Part.Parent)))) do
                        HeartbeatEvent:Wait()
                        Character = LocalPlayer.Character
                        Part = FindPart(Character, "HumanoidRootPart", "BasePart") or (FindPart(Character, "Torso", "BasePart") or FindPart(Character, "UpperTorso", "BasePart"))
                    end
                    if IsActive and IsEnabled then
                        local OriginalVelocity = Part.Velocity
                        Part.Velocity = OriginalVelocity * 100 + Vector3.new(10000, 10000, 0)
                        Part.CFrame = Part.CFrame * CFrame.new(0, 0.001, 0)
                        RenderSteppedEvent:Wait()
                        if Character and (Character.Parent and (Part and Part.Parent)) then
                            Part.Velocity = OriginalVelocity
                        end
                        SteppedEvent:Wait()
                        if Character and (Character.Parent and (Part and Part.Parent)) then
                            Part.Velocity = OriginalVelocity + Vector3.new(0, VelocityMultiplier, 0)
                            VelocityMultiplier = VelocityMultiplier * - 1
                        end
                    end
                end
            end
        end)
        if LocalPlayer.Character.Humanoid.RigType ~= Enum.HumanoidRigType.R15 then
            AnimationId = "218504594"
        else
            AnimationId = "674871189"
        end
        local Animation = Instance.new("Animation")
        Animation.AnimationId = "rbxassetid://" .. AnimationId
        local LoadedAnimation = LocalPlayer.Character.Humanoid:LoadAnimation(Animation)
        local Tool = Instance.new("Tool", LocalPlayer.Backpack)
        Tool.RequiresHandle = false
        Tool.Name = "Punch Fling"
        Tool.TextureId = "rbxassetid://3836615692"
        Tool.Activated:Connect(function()
            LoadedAnimation:Play()
            IsEnabled = true
            wait(2)
            IsEnabled = false
        end)
        Humanoid.Died:Connect(function()
            IsActive = false
            Tool:Destroy()
            Animation:Destroy()
        end)
    end
})

flingActive = false
flingMode = 1
currentInput = ""
processedPlayers = {}
roles = {}
Murder = nil
Sheriff = nil
Hero = nil

function IsAlive(Player)
    for i, v in pairs(roles) do
        if Player.Name == i then
            if not v.Killed and not v.Dead then
                return true
            else
                return false
            end
        end
    end
    return false
end

function updateRoles()
    local success, result = pcall(GetPlayerData)
    if success and result then
        roles = result
        Murder = nil
        Sheriff = nil
        Hero = nil
        for i, v in pairs(roles) do
            if v.Role == "Murderer" then
                Murder = i
            elseif v.Role == 'Sheriff' then
                Sheriff = i
            elseif v.Role == 'Hero' then
                Hero = i
            end
        end
    end
end

RunService.RenderStepped:Connect(function()
    updateRoles()
end)

function sortPlayersAlphabetically(players)
    table.sort(players, function(a, b)
        return string.lower(a.Name) < string.lower(b.Name)
    end)
    return players
end

function getPlayers(input)
    local players = {}
    input = string.lower(input or "")

    if input == "all" then
        for _, player in ipairs(Players:GetPlayers()) do
            if player ~= LocalPlayer then
                table.insert(players, player)
            end
        end
        players = sortPlayersAlphabetically(players)
    elseif input == "nonfriends" then
        for _, player in ipairs(Players:GetPlayers()) do
            if player ~= LocalPlayer then
                local success, isFriend = pcall(function()
                    return player:IsFriendsWith(LocalPlayer.UserId)
                end)
                if not (success and isFriend) then
                    table.insert(players, player)
                end
            end
        end
        players = sortPlayersAlphabetically(players)
    elseif input == "murder" then
        if Murder then
            local murdererPlayer = Players:FindFirstChild(Murder)
            if murdererPlayer and murdererPlayer ~= LocalPlayer and murdererPlayer.Character and IsAlive(murdererPlayer) then
                table.insert(players, murdererPlayer)
            end
        end
    elseif input == "sheriff" or input == "hero" then
        if Sheriff then
            local sheriffPlayer = Players:FindFirstChild(Sheriff)
            if sheriffPlayer and sheriffPlayer ~= LocalPlayer and sheriffPlayer.Character and IsAlive(sheriffPlayer) then
                table.insert(players, sheriffPlayer)
            end
        end
        if Hero then
            local heroPlayer = Players:FindFirstChild(Hero)
            if heroPlayer and heroPlayer ~= LocalPlayer and heroPlayer.Character and IsAlive(heroPlayer) then
                table.insert(players, heroPlayer)
            end
        end
    else
        local searchTerms = {}
        for term in string.gmatch(input, "([^,]+)") do
            term = string.match(term, "^%s*(.-)%s*$")
            if term ~= "" then
                table.insert(searchTerms, term)
            end
        end

        for _, player in ipairs(Players:GetPlayers()) do
            if player ~= LocalPlayer then
                local playerName = string.lower(player.Name)
                local displayName = player.DisplayName and string.lower(player.DisplayName) or ""

                for _, term in ipairs(searchTerms) do
                    if string.find(playerName, term) or string.find(displayName, term) then
                        table.insert(players, player)
                        break
                    end
                end
            end
        end
    end

    return players
end

function SkidFling(TargetPlayer, duration)
    local startTime = tick()
    local Character = LocalPlayer.Character
    local Humanoid = Character and Character:FindFirstChildOfClass("Humanoid")
    local RootPart = Humanoid and Humanoid.RootPart

    local TCharacter = TargetPlayer.Character
    local THumanoid
    local TRootPart
    local THead
    local Accessory
    local Handle

    if TCharacter:FindFirstChildOfClass("Humanoid") then
        THumanoid = TCharacter:FindFirstChildOfClass("Humanoid")
    end
    if THumanoid and THumanoid.RootPart then
        TRootPart = THumanoid.RootPart
    end
    if TCharacter:FindFirstChild("Head") then
        THead = TCharacter.Head
    end
    if TCharacter:FindFirstChildOfClass("Accessory") then
        Accessory = TCharacter:FindFirstChildOfClass("Accessory")
    end
    if Accessory and Accessory:FindFirstChild("Handle") then
        Handle = Accessory.Handle
    end

    if Character and Humanoid and RootPart then
        if RootPart.Velocity.Magnitude < 50 then
            getgenv().OldPos = RootPart.CFrame
        end
        if THead then
            workspace.CurrentCamera.CameraSubject = THead
        elseif not THead and Handle then
            workspace.CurrentCamera.CameraSubject = Handle
        elseif THumanoid and TRootPart then
            workspace.CurrentCamera.CameraSubject = THumanoid
        end
        if not TCharacter:FindFirstChildWhichIsA("BasePart") then
            return
        end

        local FPos = function(BasePart, Pos, Ang)
            RootPart.CFrame = CFrame.new(BasePart.Position) * Pos * Ang
            Character:SetPrimaryPartCFrame(CFrame.new(BasePart.Position) * Pos * Ang)
            RootPart.Velocity = Vector3.new(9e7, 9e7 * 10, 9e7)
            RootPart.RotVelocity = Vector3.new(9e8, 9e8, 9e8)
        end

        local SFBasePart = function(BasePart)
            local TimeToWait = duration or 2
            local Time = tick()
            local Angle = 0

            repeat
                if RootPart and THumanoid then
                    if BasePart.Velocity.Magnitude < 50 then
                        Angle = Angle + 100

                        FPos(BasePart, CFrame.new(0, 1.5, 0) + THumanoid.MoveDirection * BasePart.Velocity.Magnitude / 1.25, CFrame.Angles(math.rad(Angle),0 ,0))
                        task.wait()

                        FPos(BasePart, CFrame.new(0, -1.5, 0) + THumanoid.MoveDirection * BasePart.Velocity.Magnitude / 1.25, CFrame.Angles(math.rad(Angle), 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(2.25, 1.5, -2.25) + THumanoid.MoveDirection * BasePart.Velocity.Magnitude / 1.25, CFrame.Angles(math.rad(Angle), 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(-2.25, -1.5, 2.25) + THumanoid.MoveDirection * BasePart.Velocity.Magnitude / 1.25, CFrame.Angles(math.rad(Angle), 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(0, 1.5, 0) + THumanoid.MoveDirection,CFrame.Angles(math.rad(Angle), 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(0, -1.5, 0) + THumanoid.MoveDirection,CFrame.Angles(math.rad(Angle), 0, 0))
                        task.wait()
                    else
                        FPos(BasePart, CFrame.new(0, 1.5, THumanoid.WalkSpeed), CFrame.Angles(math.rad(90), 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(0, -1.5, -THumanoid.WalkSpeed), CFrame.Angles(0, 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(0, 1.5, THumanoid.WalkSpeed), CFrame.Angles(math.rad(90), 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(0, 1.5, TRootPart.Velocity.Magnitude / 1.25), CFrame.Angles(math.rad(90), 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(0, -1.5, -TRootPart.Velocity.Magnitude / 1.25), CFrame.Angles(0, 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(0, 1.5, TRootPart.Velocity.Magnitude / 1.25), CFrame.Angles(math.rad(90), 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(0, -1.5, 0), CFrame.Angles(math.rad(90), 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(0, -1.5, 0), CFrame.Angles(0, 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(0, -1.5 ,0), CFrame.Angles(math.rad(-90), 0, 0))
                        task.wait()

                        FPos(BasePart, CFrame.new(0, -1.5, 0), CFrame.Angles(0, 0, 0))
                        task.wait()
                    end
                else
                    break
                end
            until not flingActive or BasePart.Velocity.Magnitude > 500 or BasePart.Parent ~= TargetPlayer.Character or TargetPlayer.Parent ~= Players or not TargetPlayer.Character == TCharacter or THumanoid.Sit or tick() > Time + TimeToWait
        end

        local previousDestroyHeight = workspace.FallenPartsDestroyHeight
        workspace.FallenPartsDestroyHeight = 0/0

        local BV = Instance.new("BodyVelocity")
        BV.Name = "EpixVel"
        BV.Parent = RootPart
        BV.Velocity = Vector3.new(9e8, 9e8, 9e8)
        BV.MaxForce = Vector3.new(1/0, 1/0, 1/0)

        Humanoid:SetStateEnabled(Enum.HumanoidStateType.Seated, false)

        if TRootPart and THead then
            if (TRootPart.CFrame.p - THead.CFrame.p).Magnitude > 5 then
                SFBasePart(THead)
            else
                SFBasePart(TRootPart)
            end
        elseif TRootPart and not THead then
            SFBasePart(TRootPart)
        elseif not TRootPart and THead then
            SFBasePart(THead)
        elseif not TRootPart and not THead and Accessory and Handle then
            SFBasePart(Handle)
        end

        BV:Destroy()
        Humanoid:SetStateEnabled(Enum.HumanoidStateType.Seated, true)
        workspace.CurrentCamera.CameraSubject = Humanoid

        repeat
            if Character and Humanoid and RootPart and getgenv().OldPos then
                RootPart.CFrame = getgenv().OldPos * CFrame.new(0, .5, 0)
                Character:SetPrimaryPartCFrame(getgenv().OldPos * CFrame.new(0, .5, 0))
                Humanoid:ChangeState("GettingUp")
                for _, x in pairs(Character:GetChildren()) do
                    if x:IsA("BasePart") then
                        x.Velocity, x.RotVelocity = Vector3.new(), Vector3.new()
                    end
                end
            end
            task.wait()
        until not flingActive or (RootPart and getgenv().OldPos and (RootPart.Position - getgenv().OldPos.p).Magnitude < 25)
        workspace.FallenPartsDestroyHeight = previousDestroyHeight
    end
end

function shhhlol(TargetPlayer)
    local Character = LocalPlayer.Character
    local Humanoid = Character and Character:FindFirstChildOfClass("Humanoid")
    local RootPart = Humanoid and Humanoid.RootPart

    local TCharacter = TargetPlayer.Character
    local THumanoid = TCharacter and TCharacter:FindFirstChildOfClass("Humanoid")
    local TRootPart = THumanoid and THumanoid.RootPart
    local THead = TCharacter and TCharacter:FindFirstChild("Head")

    if Character and Humanoid and RootPart then
        if RootPart.Velocity.Magnitude < 50 then
            getgenv().OldPos = RootPart.CFrame
        end

        if not TCharacter:FindFirstChildWhichIsA("BasePart") then return end

        function mmmm(comkid, Pos, Ang)
            RootPart.CFrame = CFrame.new(comkid.Position) * Pos * Ang
            RootPart.RotVelocity = Vector3.new(9e8, 9e8, 9e8)
        end

        function wtf(comkid)
            local TimeToWait = 0.134
            local Time = tick()

            local Att1 = Instance.new("Attachment", RootPart)
            local Att2 = Instance.new("Attachment", comkid)

            repeat
                if RootPart and THumanoid then
                    if comkid.Velocity.Magnitude < 30 then
                        mmmm(
                            comkid,
                            CFrame.new(0, 1.5, 0) + THumanoid.MoveDirection * comkid.Velocity.Magnitude / 5,
                            CFrame.Angles(
                                math.random(1, 2) == 1 and math.rad(0) or math.rad(180),
                                math.random(1, 2) == 1 and math.rad(0) or math.rad(180),
                                math.random(1, 2) == 1 and math.rad(0) or math.rad(180)
                            )
                        )
                        task.wait()

                        mmmm(
                            comkid,
                            CFrame.new(0, 1.5, 0) + THumanoid.MoveDirection * comkid.Velocity.Magnitude / 1.25,
                            CFrame.Angles(
                                math.random(1, 2) == 1 and math.rad(0) or math.rad(180),
                                math.random(1, 2) == 1 and math.rad(0) or math.rad(180),
                                math.random(1, 2) == 1 and math.rad(0) or math.rad(180)
                            )
                        )
                        task.wait()

                        mmmm(
                            comkid,
                            CFrame.new(0, -1.5, 0) + THumanoid.MoveDirection * comkid.Velocity.Magnitude / 1.25,
                            CFrame.Angles(
                                math.random(1, 2) == 1 and math.rad(0) or math.rad(180),
                                math.random(1, 2) == 1 and math.rad(0) or math.rad(180),
                                math.random(1, 2) == 1 and math.rad(0) or math.rad(180)
                            )
                        )
                        task.wait()
                    else
                        mmmm(comkid, CFrame.new(0, -1.5, 0), CFrame.Angles(math.rad(0), 0, 0))
                        task.wait()
                    end
                else
                    break
                end
            until comkid.Velocity.Magnitude > 1000 or 
                  comkid.Parent ~= TargetPlayer.Character or
                  TargetPlayer.Parent ~= Players or
                  not TargetPlayer.Character == TCharacter or
                  Humanoid.Health <= 0 or
                  tick() > Time + TimeToWait or
                  not flingActive

            Att1:Destroy()
            Att2:Destroy()
        end

        local previousDestroyHeight = workspace.FallenPartsDestroyHeight
        workspace.FallenPartsDestroyHeight = 0/0

        local BV = Instance.new("BodyVelocity")
        BV.Parent = RootPart
        BV.Velocity = Vector3.new(-9e99, 9e99, -9e99)
        BV.MaxForce = Vector3.new(-9e9, 9e9, -9e9)

        local BodyGyro = Instance.new("BodyGyro")
        BodyGyro.CFrame = CFrame.new(RootPart.Position)
        BodyGyro.D = 9e8
        BodyGyro.MaxTorque = Vector3.new(-9e9, 9e9, -9e9)
        BodyGyro.P = -9e9

        local BodyPosition = Instance.new("BodyPosition")
        BodyPosition.Position = RootPart.Position
        BodyPosition.D = 9e8
        BodyPosition.MaxForce = Vector3.new(-9e9, 9e9, -9e9)
        BodyPosition.P = -9e9

        if TRootPart and THead then
            if (TRootPart.CFrame.p - THead.CFrame.p).Magnitude > 5 then
                wtf(THead)
            else
                wtf(TRootPart)
            end
        elseif TRootPart and not THead then
            wtf(TRootPart)
        elseif not TRootPart and THead then
            wtf(THead)
        end

        BV:Destroy()
        BodyGyro:Destroy()
        BodyPosition:Destroy()

        repeat
            if Character and Humanoid and RootPart and getgenv().OldPos then
                RootPart.CFrame = getgenv().OldPos * CFrame.new(0, .5, 0)
                Character:SetPrimaryPartCFrame(getgenv().OldPos * CFrame.new(0, .5, 0))
                Humanoid:ChangeState("GettingUp")
                for _, x in pairs(Character:GetDescendants()) do
                    if x:IsA("BasePart") then
                        x.Velocity, x.RotVelocity = Vector3.new(), Vector3.new()
                    end
                end
            end
            task.wait()
        until not flingActive or (RootPart and getgenv().OldPos and (RootPart.Position - getgenv().OldPos.p).Magnitude < 25)

        workspace.FallenPartsDestroyHeight = previousDestroyHeight
    end
end

function yeet(targetPlayer)
    local character = LocalPlayer.Character
    local targetCharacter = targetPlayer.Character

    if not character or not targetCharacter or not targetCharacter:FindFirstChild("HumanoidRootPart") then
        return false
    end

    if character.HumanoidRootPart.Velocity.Magnitude < 50 then
        getgenv().OldPos = character.HumanoidRootPart.CFrame
    end

    local existingForce = character.HumanoidRootPart:FindFirstChild("YeetForce")
    if existingForce then
        existingForce:Destroy()
    end

    local Thrust = Instance.new('BodyThrust', character.HumanoidRootPart)
    Thrust.Force = Vector3.new(9999, 9999, 9999)
    Thrust.Name = "YeetForce"

    local previousDestroyHeight = workspace.FallenPartsDestroyHeight
    workspace.FallenPartsDestroyHeight = 0/0

    local startTime = tick()
    local duration = (currentInput == "all" or currentInput == "nonfriends") and 5 or math.huge

    local yeetConnection
    yeetConnection = RunService.Heartbeat:Connect(function()
        if not targetCharacter or not targetCharacter:FindFirstChild("HumanoidRootPart") or not flingActive or tick() > startTime + duration then
            yeetConnection:Disconnect()
            Thrust:Destroy()
            workspace.FallenPartsDestroyHeight = previousDestroyHeight

            if character and character.HumanoidRootPart and getgenv().OldPos then
                character.HumanoidRootPart.CFrame = getgenv().OldPos * CFrame.new(0, .5, 0)
                character.Humanoid:ChangeState("GettingUp")
                for _, x in pairs(character:GetDescendants()) do
                    if x:IsA("BasePart") then
                        x.Velocity, x.RotVelocity = Vector3.new(), Vector3.new()
                    end
                end
            end
            return
        end

        local targetHRP = targetCharacter.HumanoidRootPart
        local targetVelocity = targetHRP.Velocity
        local speed = targetVelocity.Magnitude
        local direction = targetVelocity.Unit

        local offsetPosition
        if speed > 0.1 then
            offsetPosition = targetHRP.Position + (direction * speed)
        else
            offsetPosition = targetHRP.Position + Vector3.new(0, 0, 0)
        end

        character.HumanoidRootPart.CFrame = CFrame.new(offsetPosition)

        Thrust.Location = targetHRP.Position
    end)

    return true
end

function flingPlayers()
    local players = {}
    for player, _ in pairs(processedPlayers) do
        if player and player.Character and player.Character.Parent ~= nil then
            table.insert(players, player)
        end
    end

    if currentInput == "all" or currentInput == "nonfriends" then
        players = sortPlayersAlphabetically(players)
    end

    for _, player in ipairs(players) do
        if not flingActive then break end

        if player and player.Character and player.Character.Parent ~= nil then
            local duration = (currentInput == "all" or currentInput == "nonfriends") and 1.5 or nil

            if flingMode == 1 then
                SkidFling(player, duration)
            elseif flingMode == 2 then
                shhhlol(player)
            elseif flingMode == 3 then
                yeet(player)
                if currentInput == "all" or currentInput == "nonfriends" then
                    task.wait(1.5)
                end
            end
        end
    end

    if flingActive then
        task.wait()
        flingPlayers()
    end
end

function addPlayerToProcessed(player)
    if not player or player == LocalPlayer then return end

    local matchesFilter = false
    local input = string.lower(currentInput)

    if input == "all" then
        matchesFilter = true
    elseif input == "nonfriends" then
        local success, isFriend = pcall(function()
            return player:IsFriendsWith(LocalPlayer.UserId)
        end)
        matchesFilter = not (success and isFriend)
    elseif input == "murder" then
        if Murder and player.Name == Murder then
            matchesFilter = IsAlive(player)
        end
    elseif input == "sheriff" or input == "hero" then
        if (Sheriff and player.Name == Sheriff) or (Hero and player.Name == Hero) then
            matchesFilter = IsAlive(player)
        end
    else
        local searchTerms = {}
        for term in string.gmatch(input, "([^,]+)") do
            term = string.match(term, "^%s*(.-)%s*$")
            if term ~= "" then
                table.insert(searchTerms, term)
            end
        end

        local playerName = string.lower(player.Name)
        local displayName = player.DisplayName and string.lower(player.DisplayName) or ""

        for _, term in ipairs(searchTerms) do
            if string.find(playerName, term) or string.find(displayName, term) then
                matchesFilter = true
                break
            end
        end
    end

    if matchesFilter then
        processedPlayers[player] = true
    end
end

flingInputValue = ""

function FlingRole(RoleName)
    if flingActive then
        flingActive = false
        return
    end

    local playersWithRole = {}
    updateRoles()

    for playerName, playerData in pairs(roles) do
        if playerData.Role == RoleName then
            local player = Players:FindFirstChild(playerName)
            if player and player ~= LocalPlayer and player.Character and IsAlive(player) then
                table.insert(playersWithRole, player)
            end
        end
    end

    if #playersWithRole == 0 then
        Fluent:Notify({
            Title = "Fling Role",
            Content = "No " .. RoleName .. " found",
            Duration = 3
        })
        return false
    end

    currentInput = RoleName
    flingActive = true
    processedPlayers = {}

    for _, player in ipairs(playersWithRole) do
        processedPlayers[player] = true
    end

    local targetNames = ""
    for i, player in ipairs(playersWithRole) do
        targetNames = targetNames .. player.Name
        if i < #playersWithRole then
            targetNames = targetNames .. ", "
        end
    end

    Fluent:Notify({
        Title = "Fling Role",
        Content = "Flinging " .. RoleName .. ": " .. targetNames .. " for 10 seconds",
        Duration = 3
    })

    local stopTimer = 10
    local startTime = tick()

    coroutine.wrap(function()
        while flingActive and tick() - startTime < stopTimer do
            task.wait(1)
        end
        if flingActive then
            flingActive = false
            processedPlayers = {}
            if FlingToggle then
                FlingToggle:SetValue(false)
            end
        end
    end)()

    coroutine.wrap(flingPlayers)()
    return true
end


Tabs.Utility:AddSpace({ Height = 20 })
secUtilityFlingSettings = Tabs.Utility:AddSection("Fling Settings", "solar/settings-bold")

FlingInput = secUtilityFlingSettings:AddInput("FlingInput", {
    Title = "Fling Target",
    Placeholder = "nickname, all, nonfriends, murder, sheriff",
    Callback = function(value)
        flingInputValue = value
        currentInput = string.lower(value)
    end
})

FlingModeDropdown = secUtilityFlingSettings:AddDropdown("FlingModeDropdown", {
    Search = false,
    Title = "Fling Mode",
    Values = {"SkidFling", "Shhhlol", "Yeet"},
    Default = "SkidFling",
    Callback = function(value)
        if value == "SkidFling" then
            flingMode = 1
        elseif value == "Shhhlol" then
            flingMode = 2
        elseif value == "Yeet" then
            flingMode = 3
        end
    end
})

FlingToggle = secUtilityFlingSettings:AddToggle("FlingToggle", {
    Title = "Fling Players",
    Default = false,
    Callback = function(DConfiguration)
        flingActive = DConfiguration

        if flingActive then
            currentInput = string.lower(flingInputValue or "")
            local players = getPlayers(currentInput)

            if #players == 0 then
                Fluent:Notify({
                    Title = "Fling Target",
                    Content = "Invalid Input: " .. currentInput,
                    Duration = 3
                })
                flingActive = false
                FlingToggle:SetValue(false)
                return
            end

            processedPlayers = {}
            for _, player in ipairs(players) do
                addPlayerToProcessed(player)
            end

            Fluent:Notify({
                Title = "Fling Target",
                Content = "Flinging " .. #players .. " players",
                Duration = 3
            })

            coroutine.wrap(flingPlayers)()
        else
            processedPlayers = {}
        end
    end
})

secUtilityFlingSettings:AddButton({
    Title = "Fling Murderer",
    Callback = function()
        FlingRole("Murderer")
    end
})

secUtilityFlingSettings:AddSpace({ Height = 20 })
secUtilityFlingSettings:AddButton({
    Title = "Fling Sheriff/Hero",
    Callback = function()
        local sheriff = GetSheriff()
        local hero = GetHero()
        if sheriff then
            FlingRole("Sheriff")
        elseif hero then
            FlingRole("Hero")
        else
            Fluent:Notify({
                Title = "Fling Role",
                Content = "No Hero or Sheriff found",
                Duration = 3
            })
        end
    end
})

Players.PlayerAdded:Connect(function(player)
    if flingActive then
        addPlayerToProcessed(player)
        if player.Character then
            if flingMode == 1 then
                local duration = (currentInput == "all" or currentInput == "nonfriends") and 1.5 or nil
                SkidFling(player, duration)
            elseif flingMode == 2 then
                shhhlol(player)
            elseif flingMode == 3 then
                yeet(player)
            end
        else
            player.CharacterAdded:Connect(function()
                if flingActive then
                    addPlayerToProcessed(player)
                    if flingMode == 1 then
                        local duration = (currentInput == "all" or currentInput == "nonfriends") and 1.5 or nil
                        SkidFling(player, duration)
                    elseif flingMode == 2 then
                        shhhlol(player)
                    elseif flingMode == 3 then
                        yeet(player)
                    end
                end
            end)
        end
    end
end)

LocalPlayer.CharacterAdded:Connect(function()
    if flingActive then
        task.wait(1)
        coroutine.wrap(flingPlayers)()
    end
end)

antiVoidActive = false
originalDestroyHeight = workspace.FallenPartsDestroyHeight

function enableAntiVoid()
    if antiVoidActive then return end
    antiVoidActive = true
    originalDestroyHeight = workspace.FallenPartsDestroyHeight
    workspace.FallenPartsDestroyHeight = -math.huge
end

function disableAntiVoid()
    if not antiVoidActive then return end
    workspace.FallenPartsDestroyHeight = originalDestroyHeight
    antiVoidActive = false
end


Tabs.Utility:AddSpace({ Height = 20 })
secUtilityAntiVoid = Tabs.Utility:AddSection("Anti Void", "solar/shield-minimalistic-bold")

secUtilityAntiVoid:AddToggle("AntiVoid", {
    Title = "Anti Void Damage",
    Default = false,
    Callback = function(DConfiguration)
        if DConfiguration then
            enableAntiVoid()
        else
            disableAntiVoid()
        end
    end
})

infinitePositionEnabled = false
savedPosition = nil
positionConnection = nil
positionTolerance = 0.1

function lockPosition()
    local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local hrp = character:WaitForChild("HumanoidRootPart", 5)
    if not hrp then return end

    if not savedPosition then
        savedPosition = hrp.CFrame
    end

    positionConnection = RunService.Heartbeat:Connect(function()
        if hrp and hrp.Parent and savedPosition then
            if (hrp.Position - savedPosition.Position).Magnitude > positionTolerance then
                hrp.CFrame = savedPosition
                hrp.Velocity = Vector3.new(0, 0, 0)
                hrp.RotVelocity = Vector3.new(0, 0, 0)
            end
        end
    end)
end

function unlockPosition()
    if positionConnection then
        positionConnection:Disconnect()
        positionConnection = nil
    end
    savedPosition = nil
end


Tabs.Utility:AddSpace({ Height = 20 })
secUtilityMisc = Tabs.Utility:AddSection("Misc", "solar/widget-2-bold")

InfinitePositionToggle = secUtilityMisc:AddToggle("InfinitePositionToggle", {
    Title = "Infinite Position Lock",
    Description = "Lock your position in place",
    Default = false,
    Callback = function(DConfiguration)
        infinitePositionEnabled = DConfiguration
        if DConfiguration then
            lockPosition()
            LocalPlayer.CharacterAdded:Connect(function()
                if infinitePositionEnabled then
                    task.wait(0.1)
                    lockPosition()
                end
            end)
        else
            unlockPosition()
        end
    end
})

TimeChangerInput = secUtilityMisc:AddInput("TimeChangerInput", {
    Title = "Set Time (HH:MM)",
    Placeholder = "12:00",
    Callback = function(value)
        value = value:gsub("^%s*(.-)%s*$", "%1")

        local h_str, m_str = value:match("(%d+):(%d+)")
        if h_str and m_str then
            local h = tonumber(h_str)
            local m = tonumber(m_str)

            if h and m and h >= 0 and h <= 23 and m >= 0 and m <= 59 and #h_str <= 2 and #m_str <= 2 then
                local totalHours = h + (m / 60)
                Lighting.ClockTime = totalHours
            end
        end
    end
})

lagSwitchEnabled = false
lagDuration = 0.5
lagMethod = "CPU Cycle"
isLagActive = false
lagSystemLoaded = false

function lag()
    local duration = lagDuration or 0.5
    local method = lagMethod or "CPU Cycle"

    if method == "CPU Cycle" then pcall(function() setfflag("MaxMissedWorldStepsRemembered","1") end)
        local start = tick()
        while tick() - start < duration do
            local a = math.random(1, 1000000) * math.random(1, 1000000)
            a = a / math.random(1, 10000)
        end
    elseif method == "OS.ClockFFlag" then
        pcall(function() setfflag("MaxMissedWorldStepsRemembered","10000001000000") end)
        local start = os.clock()
        while os.clock() - start < duration do
        end
    end
end

function loadLagSystem()
    if lagSystemLoaded then return end
    lagSystemLoaded = true
end

function unloadLagSystem()
    if not lagSystemLoaded then return end
    lagSystemLoaded = false
    isLagActive = false
end

function checkLagState()
    local shouldLoad = lagSwitchEnabled
    if shouldLoad and not lagSystemLoaded then
        loadLagSystem()
    elseif not shouldLoad and lagSystemLoaded then
        unloadLagSystem()
    end
end

Tabs.Utility:AddSpace({ Height = 20 })
secUtilityLagSwitch = Tabs.Utility:AddSection("Lag Switch", "solar/bolt-circle-bold")

LagSwitchToggle = secUtilityLagSwitch:AddToggle("LagSwitchToggle", {
    Title = "Lag Switch",
    Icon = "zap",
    Default = false,
    Callback = function(DConfiguration)
        lagSwitchEnabled = DConfiguration
        checkLagState()
    end
})

secUtilityLagSwitch:AddToggle("ShowLagSwitchButton", {
    Title = "Show Lag Switch Button",
    Default = false,
    Callback = function(DConfiguration)
        FloatingButtonModule.SetVisible(LagSwitchFrame, DConfiguration)
    end,
})

LagSwitchKeybind = secUtilityLagSwitch:AddKeybind("LagSwitchKeybind", {
    Title = "Trigger Lag Switch",
    Description = "Keybind to trigger lag switch",
    Default = "L",
    Callback = function()
        if lagSwitchEnabled and not isLagActive then
            isLagActive = true
            task.spawn(function() lag(); isLagActive = false end)
        end
    end,
})

FBModule:AddSizeInputs(secUtilityLagSwitch, "LagSwitchBtn", "Lag Switch")

LagSwitchFrame, LagSwitchButton = FBModule:Create("LagSwitchBtn", "Lag Switch", false, function()
    isLagActive = task.spawn(lag)
end)

LagMethodDropdown = secUtilityLagSwitch:AddDropdown("LagMethodDropdown", {
    Search = false,
    Title = "Lag Method",
    Values = {"CPU Cycle", "OS.ClockFFlag"},
    Default = "CPU Cycle",
    Callback = function(value)
        lagMethod = value
    end
})

secUtilityLagSwitch:AddSpace({ Height = 20 })
LagDurationInput = secUtilityLagSwitch:AddInput("LagDurationInput", {
    Title = "Lag Duration (seconds)",
    Placeholder = "0.5",
    Default = tostring(lagDuration),
    Numeric = true,
    Callback = function(text)
        local n = tonumber(text)
        if n and n > 0 then
            lagDuration = n
        end
    end
})

Players.PlayerRemoving:Connect(function(leavingPlayer)
    if leavingPlayer == LocalPlayer then
        unloadLagSystem()
    end
end)

checkLagState()

originalGameGravity = workspace.Gravity
CustomGravity = false
GravityValue = originalGameGravity
ShowGravityButton = false



Tabs.Utility:AddSpace({ Height = 20 })
secUtilityGravity = Tabs.Utility:AddSection("Gravity", "solar/planet-bold")

GravityToggle = secUtilityGravity:AddToggle("GravityToggle", {
    Title = "Custom Gravity",
    Default = false,
    Callback = function(DConfiguration)
        CustomGravity = DConfiguration
        workspace.Gravity = DConfiguration and GravityValue or originalGameGravity
    end
})

ShowGravityButtonToggle = secUtilityGravity:AddToggle("ShowGravityButton", {
    Title = "Show Gravity Button",
    Default = false,
    Callback = function(DConfiguration)
        ShowGravityButton = DConfiguration
        FloatingButtonModule.SetVisible(GravityFrame, DConfiguration)
    end,
})

GravityKeybind = secUtilityGravity:AddKeybind("GravityKeybind", {
    Title = "Toggle Gravity",
    Description = "Keybind to toggle custom gravity",
    Default = "J",
    Callback = function()
        if GravityToggle then
            local newState = not CustomGravity
            GravityToggle:SetValue(newState)
            FloatingButtonModule.SetActive(GravityFloatButton, newState, "Gravity")
        end
    end,
})

FBModule:AddSizeInputs(secUtilityGravity, "GravityBtn", "Gravity")

GravityFrame, GravityFloatButton = FBModule:Create("GravityBtn", "Gravity", true, function(Btn)
    if GravityToggle then
        local newState = not CustomGravity
        GravityToggle:SetValue(newState)
        FloatingButtonModule.SetActive(Btn, newState, "Gravity")
    end
end)

GravityInput = secUtilityGravity:AddInput("GravityInput", {
    Title = "Gravity Value",
    Placeholder = tostring(originalGameGravity),
    Default = tostring(GravityValue),
    Callback = function(text)
        local num = tonumber(text)
        if num then
            GravityValue = num
            if CustomGravity then
                workspace.Gravity = num
            end
        else
            warn("Invalid gravity value entered!")
        end
    end
})

workspace.Gravity = CustomGravity and GravityValue or originalGameGravity

Gravity = Gravity or {
    CustomGravity = false,
    GravityValue = workspace.Gravity
}


Tabs.Utility:AddSpace({ Height = 20 })
secUtilityNoRender = Tabs.Utility:AddSection("No Render", "solar/eye-closed-bold")

NoRenderToggle = secUtilityNoRender:AddToggle("NoRenderToggle", {
    Title = "No Render",
    Description = "Disable 3D rendering for performance",
    Default = false,
    Callback = function(DConfiguration)
        NoRender = DConfiguration
        RunService:Set3dRenderingEnabled(not state)

        if DConfiguration then
            local gui = Instance.new("ScreenGui")
            gui.Name = "NoRenderBackground"
            gui.IgnoreGuiInset = true
            gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
            gui.ResetOnSpawn = false

            local frame = Instance.new("Frame")
            frame.Size = UDim2.new(1, 0, 1, 0)
            frame.BackgroundColor3 = NoRenderColor
            frame.BorderSizePixel = 0
            frame.Parent = gui

            gui.Parent = PlayerGui
        else
            local gui = PlayerGui:FindFirstChild("NoRenderBackground")
            if gui then
                gui:Destroy()
            end
        end
    end
})

NoRenderColorPicker = secUtilityNoRender:AddColorpicker("NoRenderColorPicker", {
    Title = "No Render Color",
    Description = "Choose background color when No Render is enabled",
    Default = Color3.fromRGB(0, 0, 0),
    Transparency = 0,
    Callback = function(color)
        NoRenderColor = color

        if NoRender then
            local gui = PlayerGui:FindFirstChild("NoRenderBackground")
            if gui then
                local frame = gui:FindFirstChildOfClass("Frame")
                if frame then
                    frame.BackgroundColor3 = color
                end
            end
        end
    end
})

RemoveTextures = false

RemoveTexturesButton = secUtilityNoRender:AddButton({
    Title = "Remove Textures",
    Callback = function()
        for _, part in ipairs(workspace:GetDescendants()) do
            if part:IsA("Part") or part:IsA("MeshPart") or part:IsA("UnionOperation") or part:IsA("WedgePart") or part:IsA("CornerWedgePart") then
                if part:IsA("Part") then
                    part.Material = Enum.Material.SmoothPlastic
                end
                if part:FindFirstChildWhichIsA("Texture") then
                    local texture = part:FindFirstChildWhichIsA("Texture")
                    texture.Texture = "rbxassetid://0"
                end
                if part:FindFirstChildWhichIsA("Decal") then
                    local decal = part:FindFirstChildWhichIsA("Decal")
                    decal.Texture = "rbxassetid://0"
                end
            end
        end
    end
})

Players.PlayerRemoving:Connect(function(leavingPlayer)
    if leavingPlayer == LocalPlayer then
        RunService:Set3dRenderingEnabled(true)
    end
end)

LowQualityButton = secUtilityNoRender:AddButton({
    Title = "Low Quality",
    Description = "Disable textures, effects, and optimize graphics",
    Callback = function()
        local ToDisable = {
            Textures = true,
            VisualEffects = true,
            Parts = true,
            Particles = true,
            Sky = true
        }

        local ToEnable = {
            FullBright = false
        }

        local Stuff = {}

        for _, v in next, game:GetDescendants() do
            if ToDisable.Parts then
                if v:IsA("Part") or v:IsA("UnionOperation") or v:IsA("BasePart") then
                    v.Material = Enum.Material.SmoothPlastic
                    table.insert(Stuff, 1, v)
                end
            end

            if ToDisable.Particles then
                if v:IsA("ParticleEmitter") or v:IsA("Smoke") or v:IsA("Explosion") or v:IsA("Sparkles") or v:IsA("Fire") then
                    v.Enabled = false
                    table.insert(Stuff, 1, v)
                end
            end

            if ToDisable.VisualEffects then
                if v:IsA("BloomEffect") or v:IsA("BlurEffect") or v:IsA("DepthOfFieldEffect") or v:IsA("SunRaysEffect") then
                    v.Enabled = false
                    table.insert(Stuff, 1, v)
                end
            end

            if ToDisable.Textures then
                if v:IsA("Decal") or v:IsA("Texture") then
                    v.Texture = ""
                    table.insert(Stuff, 1, v)
                end
            end

            if ToDisable.Sky then
                if v:IsA("Sky") then
                    v.Parent = nil
                    table.insert(Stuff, 1, v)
                end
            end
        end

        if ToEnable.FullBright then
            local Lighting = game:GetService("Lighting")

            Lighting.FogColor = Color3.fromRGB(255, 255, 255)
            Lighting.FogEnd = math.huge
            Lighting.FogStart = math.huge
            Lighting.Ambient = Color3.fromRGB(255, 255, 255)
            Lighting.Brightness = 5
            Lighting.ColorShift_Bottom = Color3.fromRGB(255, 255, 255)
            Lighting.ColorShift_Top = Color3.fromRGB(255, 255, 255)
            Lighting.OutdoorAmbient = Color3.fromRGB(255, 255, 255)
            Lighting.Outlines = true
        end
    end
})

antiFlingEnabled = false
antiFlingConnection = nil

function setCanCollideOfModelDescendants(model, bval)
    if not model then return end
    for _, v in pairs(model:GetDescendants()) do
        if v:IsA("BasePart") then
            v.CanCollide = bval
        end
    end
end

function startAntiFling()
    if antiFlingConnection then return end

    antiFlingConnection = RunService.Stepped:Connect(function()
        if antiFlingEnabled then
            for _, player in ipairs(Players:GetPlayers()) do
                if player ~= LocalPlayer and player.Character then
                    setCanCollideOfModelDescendants(player.Character, false)
                end
            end
        end
    end)
end

function stopAntiFling()
    if antiFlingConnection then
        antiFlingConnection:Disconnect()
        antiFlingConnection = nil
    end

    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character then
            setCanCollideOfModelDescendants(player.Character, true)
        end
    end
end


Tabs.Utility:AddSpace({ Height = 20 })
secUtilityAntiFling = Tabs.Utility:AddSection("Anti Fling", "solar/shield-bold")

AntiFlingToggle = secUtilityAntiFling:AddToggle("AntiFlingToggle", {
    Title = "Disable Player Collisions",
    Default = false,
    Callback = function(DConfiguration)
        antiFlingEnabled = DConfiguration
        if DConfiguration then
            startAntiFling()
        else
            stopAntiFling()
        end
    end
})

HitboxSettings = {
    Enabled = false,  
    Size = 10,
    ShowVisual = false,   
    VisualColor = Color3.new(1, 0, 0),  
    OriginalSizes = {},   
    VisualAdornments = {} 
}

function ExpandHitboxes()
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer then
            local chr = plr.Character
            if chr and HitboxSettings.Enabled then
                local root = chr:FindFirstChild("HumanoidRootPart")
                if root then
                    if HitboxSettings.OriginalSizes[plr] == nil then
                        HitboxSettings.OriginalSizes[plr] = root.Size
                    end
                    root.Size = Vector3.new(HitboxSettings.Size, HitboxSettings.Size, HitboxSettings.Size)
                end
            end
        end
    end
end

function UpdateVisualHitboxes()
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer then
            local chr = plr.Character
            local visual = HitboxSettings.VisualAdornments[plr]
            if chr and HitboxSettings.ShowVisual and HitboxSettings.Enabled then
                local root = chr:FindFirstChild("HumanoidRootPart")
                if root then
                    if not visual then
                        visual = Instance.new("BoxHandleAdornment")
                        visual.Adornee = root
                        visual.Size = Vector3.new(HitboxSettings.Size, HitboxSettings.Size, HitboxSettings.Size)
                        visual.Color3 = HitboxSettings.VisualColor
                        visual.Transparency = 0.3
                        visual.ZIndex = 10
                        visual.AlwaysOnTop = true
                        visual.Parent = root
                        HitboxSettings.VisualAdornments[plr] = visual
                    else
                        visual.Size = Vector3.new(HitboxSettings.Size, HitboxSettings.Size, HitboxSettings.Size)
                        visual.Color3 = HitboxSettings.VisualColor
                    end
                end
            elseif visual then
                visual:Destroy()
                HitboxSettings.VisualAdornments[plr] = nil
            end
        end
    end
end

function ResetHitboxes()
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer then
            local chr = plr.Character
            if chr then
                local root = chr:FindFirstChild("HumanoidRootPart")
                if root and HitboxSettings.OriginalSizes[plr] then
                    root.Size = HitboxSettings.OriginalSizes[plr]
                elseif root then
                    root.Size = Vector3.new(3, 3, 3) 
                end
            end
        end
    end
    HitboxSettings.OriginalSizes = {}
end

function ClearVisualAdornments()
    for _, visual in pairs(HitboxSettings.VisualAdornments) do
        if visual then
            pcall(function() visual:Destroy() end)
        end
    end
    HitboxSettings.VisualAdornments = {}
end

Players.PlayerAdded:Connect(function(plr)
    if HitboxSettings.Enabled then
        task.wait(0.5)
        ExpandHitboxes()
        if HitboxSettings.ShowVisual then
            UpdateVisualHitboxes()
        end
    end
    plr.CharacterAdded:Connect(function()
        if HitboxSettings.Enabled then
            task.wait(0.5)
            ExpandHitboxes()
            if HitboxSettings.ShowVisual then
                UpdateVisualHitboxes()
            end
        end
    end)
end)

Players.PlayerRemoving:Connect(function(plr)
    HitboxSettings.OriginalSizes[plr] = nil
    if HitboxSettings.VisualAdornments[plr] then
        HitboxSettings.VisualAdornments[plr]:Destroy()
        HitboxSettings.VisualAdornments[plr] = nil
    end
end)

for _, plr in ipairs(Players:GetPlayers()) do
    if plr ~= LocalPlayer then
        plr.CharacterAdded:Connect(function()
            if HitboxSettings.Enabled then
                task.wait(0.5)
                ExpandHitboxes()
                if HitboxSettings.ShowVisual then
                    UpdateVisualHitboxes()
                end
            end
        end)
    end
end

RunService.Heartbeat:Connect(function()
    if HitboxSettings.Enabled then
        ExpandHitboxes()
        if HitboxSettings.ShowVisual then
            UpdateVisualHitboxes()
        end
    end
end)


Tabs.Utility:AddSpace({ Height = 20 })
secUtilityHitbox = Tabs.Utility:AddSection("Hitbox", "solar/target-bold")

secUtilityHitbox:AddToggle("HRPscaler", {
    Title = "Hitbox Expanding",
    Callback = function(DConfiguration)
        HitboxSettings.Enabled = DConfiguration
        if DConfiguration then
            ExpandHitboxes()
            if HitboxSettings.ShowVisual then
                UpdateVisualHitboxes()
            end
        else
            ResetHitboxes()
            ClearVisualAdornments()
        end
    end
})

secUtilityHitbox:AddSlider("HRPsize", {
    Title = "Hitbox Size",
    Min = 3,
    Max = 30,
    Default = 10,
    Rounding = 0,
    Callback = function(val)
        HitboxSettings.Size = tonumber(val) or 10
        if HitboxSettings.Enabled then
            ExpandHitboxes()
            if HitboxSettings.ShowVisual then
                UpdateVisualHitboxes()
            end
        end
    end
})

secUtilityHitbox:AddToggle("ShowHRP", {
    Title = "Show Hitbox",
    Type = "Checkbox",
    Callback = function(DConfiguration)
        HitboxSettings.ShowVisual = DConfiguration
        if DConfiguration and HitboxSettings.Enabled then
            UpdateVisualHitboxes()
        elseif not DConfiguration then
            ClearVisualAdornments()
        end
    end
})

secUtilityHitbox:AddColorpicker("UtilityColorpicker167", {
    Title = "Hitbox Color",
    Default = Color3.new(1, 0, 0),
    Callback = function(col)
        HitboxSettings.VisualColor = col
        if HitboxSettings.ShowVisual and HitboxSettings.Enabled then
            UpdateVisualHitboxes()
        end
    end
})

if a then
    local v1, v2, v3 = pairs(a)
    while true do
        local v4
        v3, v4 = v1(v2, v3)
        if v3 == nil then
            break
        end
        v4:Disconnect()
    end
    a = nil
end

repeat
    task.wait()
until LocalPlayer

vu5 = LocalPlayer
vu6 = nil
vu7 = nil
vu8 = nil
vu9 = false
vu10 = {}

function vu16()
    vu6 = vu5.Character or vu5.CharacterAdded:Wait()
    vu7 = vu6:WaitForChild("Humanoid")
    vu8 = vu6:WaitForChild("HumanoidRootPart")
    vu10 = {}
    local v11 = vu6
    local v12, v13, v14 = pairs(v11:GetDescendants())
    while true do
        local v15 = nil
        v14, v15 = v12(v13, v14)
        if v14 == nil then
            break
        end
        if v15:IsA("BasePart") and v15.Transparency == 0 then
            vu10[#vu10 + 1] = v15
        end
    end
end

function vu30()
    InvisibleFrame, InvisibleFloatButton = FBModule:Create("InvisibleBtn", "INVISIBLE", true, function(Btn)
        vu9 = not vu9
        FloatingButtonModule.SetActive(Btn, vu9, "Invisible")
        if vu9 then
            local v26, v27, v28 = pairs(vu10)
            while true do
                local v29 = nil
                v28, v29 = v26(v27, v28)
                if v28 == nil then
                    break
                end
                v29.Transparency = v29.Transparency == 0 and 0.5 or 0
            end
        else
            local v26, v27, v28 = pairs(vu10)
            while true do
                local v29 = nil
                v28, v29 = v26(v27, v28)
                if v28 == nil then
                    break
                end
                v29.Transparency = 0
            end
        end
    end)

    InvisibleToggleElement = {
        SetValue = function(_, DConfiguration)
            vu9 = DConfiguration
            FloatingButtonModule.SetActive(InvisibleFloatButton, vu9, "Invisible")
        end,
    }
end

vu16()
vu30()

v31 = {
    nil,
    nil
}
v32 = vu5

v31[1] = vu5:GetMouse().KeyDown:Connect(function(p33)
    if p33 == "i" then
        vu9 = not vu9

        FloatingButtonModule.SetActive(InvisibleFloatButton, vu9, "Invisible")

        local v34, v35, v36 = pairs(vu10)
        while true do
            local v37 = nil
            v36, v37 = v34(v35, v36)
            if v36 == nil then
                break
            end
            if vu9 then
                v37.Transparency = v37.Transparency == 0 and 0.5 or 0
            else
                v37.Transparency = 0
            end
        end
    end
end)

v31[2] = RunService.Heartbeat:Connect(function()
    if vu9 then
        local v38 = vu8.CFrame
        local v39 = vu7.CameraOffset
        local v40 = v38 * CFrame.new(0, 50000, 0)
        local v41 = vu7
        local v42 = vu8
        local v43 = v40:ToObjectSpace(CFrame.new(v38.Position)).Position
        v42.CFrame = v40
        v41.CameraOffset = v43
        RunService.RenderStepped:Wait()
        local v44 = vu7
        vu8.CFrame = v38
        v44.CameraOffset = v39
    end
end)

vu5.CharacterAdded:Connect(function()
    vu9 = false

    FloatingButtonModule.SetActive(InvisibleFloatButton, false, "Invisible")

    vu16()
end)


Tabs.Utility:AddSpace({ Height = 20 })
secUtilityInvisible = Tabs.Utility:AddSection("Invisible", "solar/ghost-bold")

InvisibleGuiToggle = secUtilityInvisible:AddToggle("InvisibleGuiToggle", {
    Title = "Show Invisible Button",
    Default = false,
    Callback = function(DConfiguration)
        FloatingButtonModule.SetVisible(InvisibleFrame, DConfiguration)
    end,
})

secUtilityInvisible:AddKeybind("InvisibleKeybind", {
    Title = "Invisible Toggle",
    Description = "Keybind to toggle invisible mode",
    Default = "I",
    Callback = function()
        vu9 = not vu9
        FloatingButtonModule.SetActive(InvisibleFloatButton, vu9, "Invisible")
        for _, part in pairs(vu10) do
            part.Transparency = vu9 and 0.5 or 0
        end
    end,
})

FBModule:AddSizeInputs(secUtilityInvisible, "InvisibleBtn", "Invisible")


do
    local function GetPing()
        local n = Stats:FindFirstChild("Network")
        if not n then return 0 end
        local s = n:FindFirstChild("ServerStatsItem")
        if not s then return 0 end
        local p = s:FindFirstChild("Data Ping")
        if not p then return 0 end
        return math.floor(p:GetValue())
    end

    local function CreateFpsCounter()
        local FpsCounterGui = Instance.new("ScreenGui")
        FpsCounterGui.Name = "FPSCounter"
        FpsCounterGui.Parent = CoreGui
        FpsCounterGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
        FpsCounterGui.ResetOnSpawn = false

        local FpsFrame = Instance.new("Frame")
        FpsFrame.Parent = FpsCounterGui
        FpsFrame.Size = UDim2.new(0, 180, 0, 80)
        FpsFrame.Position = UDim2.new(0, 300, 0, 10)
        FpsFrame.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
        FpsFrame.BackgroundTransparency = 0.7
        Instance.new("UICorner", FpsFrame).CornerRadius = UDim.new(0, 15)

        local FpsGradient = Instance.new("UIGradient")
        FpsGradient.Parent = FpsFrame

        local FpsStroke = Instance.new("UIStroke")
        FpsStroke.Thickness = 2
        FpsStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
        FpsStroke.Parent = FpsFrame

        local FpsStrokeGrad = Instance.new("UIGradient")
        FpsStrokeGrad.Parent = FpsStroke

        local function UpdateColors()
            local Grad = Fluent:GetButtonGradient() or Fluent.ButtonGradients
            if Grad then
                if Grad.Background then
                    FpsGradient.Color = Grad.Background
                end
                if Grad.Stroke then
                    FpsStrokeGrad.Color = Grad.Stroke
                end
            end
        end

        UpdateColors()

        local RotationTask = task.spawn(function()
            while FpsFrame.Parent do
                FpsGradient.Rotation = (FpsGradient.Rotation + 1) % 360
                FpsStrokeGrad.Rotation = (FpsStrokeGrad.Rotation + 0.5) % 360
                UpdateColors()
                task.wait(0.03)
            end
        end)

        local FpsLabel = Instance.new("TextLabel")
        FpsLabel.Parent = FpsFrame
        FpsLabel.Size = UDim2.new(1, -10, 1, -10)
        FpsLabel.Position = UDim2.new(0, 5, 0, 5)
        FpsLabel.BackgroundTransparency = 1
        FpsLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
        FpsLabel.Font = Enum.Font.GothamBlack
        FpsLabel.TextSize = 12
        FpsLabel.TextWrapped = true
        FpsLabel.TextXAlignment = Enum.TextXAlignment.Center
        FpsLabel.TextYAlignment = Enum.TextYAlignment.Center
        FpsLabel.Text = "FPS: 0 | Ping: 0 ms\nClient Timer: 0h 0m 0s"

        local StartTime = tick()
        local LastUpdateTime = StartTime
        local FrameCount = 0
        local PreviousText = ""

        local RenderConn = RunService.RenderStepped:Connect(function()
            FrameCount = FrameCount + 1
            local Now = tick()
            local Dt = Now - LastUpdateTime
            if Dt >= 1 then
                local Fps = math.round(FrameCount / Dt)
                local Elapsed = Now - StartTime
                local H = math.floor(Elapsed / 3600)
                local M = math.floor((Elapsed % 3600) / 60)
                local S = math.floor(Elapsed % 60)
                local Ping = GetPing()
                local Text = string.format("FPS: %d | Ping: %d ms\nClient Timer: %dh %dm %ds", Fps, Ping, H, M, S)
                if Text ~= PreviousText then
                    FpsLabel.Text = Text
                    PreviousText = Text
                end
                LastUpdateTime = Now
                FrameCount = 0
            end
        end)

        local function Cleanup()
            if RenderConn then RenderConn:Disconnect() end
            if RotationTask then task.cancel(RotationTask) end
            FpsCounterGui:Destroy()
        end

        return FpsCounterGui, Cleanup
    end

    local function RemoveFpsCounter()
        local Gui = CoreGui:FindFirstChild("FPSCounter")
        if Gui then Gui:Destroy() end
    end

    if not getgenv().FpsCounterInstance then
        local counter, cleanup = CreateFpsCounter()
        getgenv().FpsCounterInstance = counter
        getgenv().FpsCounterCleanup = cleanup
    else
        RemoveFpsCounter()
        local counter, cleanup = CreateFpsCounter()
        getgenv().FpsCounterInstance = counter
        getgenv().FpsCounterCleanup = cleanup
    end

    local secSettingsFps = Tabs.Settings:AddSection("FPS Counter", "solar/gauge-bold")
    secSettingsFps:AddToggle("FPSCounterToggle", {
        Title = "Show FPS Counter",
        Default = true,
        Callback = function(DConfiguration)
            local counter = getgenv().FpsCounterInstance
            if counter then
                counter.Enabled = DConfiguration
            end
        end
    })
end

do
    local GoonWaresFolder = CoreGui:FindFirstChild("GoonWares")

    if GoonWaresFolder and Tabs and Tabs.Settings then
        Tabs.Settings:AddSpace({ Height = 20 })
        local secSettingsAuto2 = Tabs.Settings:AddSection("GUI Size", "solar/widget-2-bold")
        local defaultScales = {}

        for _, Element in pairs(GoonWaresFolder:GetChildren()) do
            if Element:IsA("Frame") and Element:FindFirstChild("UIScale") then
                defaultScales[Element.Name] = Element.UIScale.Scale
            end
        end

        secSettingsAuto2:AddButton({
            Title = "Reset All Scales",
            Description = "Reverts all buttons to their startup scale values",
            Callback = function()
                for _, Element in pairs(GoonWaresFolder:GetChildren()) do
                    if Element:IsA("Frame") and Element:FindFirstChild("UIScale") then
                        local original = defaultScales[Element.Name] or 1
                        Element.UIScale.Scale = original
                    end
                end
            end
        })

        for _, Element in pairs(GoonWaresFolder:GetChildren()) do
            if Element:IsA("Frame") and Element:FindFirstChild("UIScale") then
                local currentScale = tonumber(Element.UIScale.Scale) or 1

                secSettingsAuto2:AddSlider("Scale_Slider_" .. Element.Name, {
                    Title = Element.Name .. " Scale",
                    Description = "Adjust GUI scale",
                    Rounding = 0.01,
                    Min = 0.01,
                    Max = 4,
                    Default = currentScale,
                    Callback = function(val)
                        if Element and Element:FindFirstChild("UIScale") then
                            Element.UIScale.Scale = tonumber(val)
                        end
                    end
                })
            end
        end
    end
end

function UniverseScriptsStuff(Tabs)
    local player = game.Players.LocalPlayer

    local function GetRoot()
        return player.Character and player.Character:FindFirstChild("HumanoidRootPart")
    end

    local MAP_ASSET_ID = 13336839729
    local ALTURA_INICIAL = 5000
    local soundReplacementEnabled = false
    local replacementSoundId = "rbxasset://sounds/oof.ogg"
    local soundReplacementConnection = nil

    local function IsRobloxAsset(id)
        return id:match("^%d+$") or id:match("^rbxasset://") or id:match("^rbxassetid://")
    end
    local function IsHttpAsset(url)
        return url:match("^https?://")
    end
    local function DownloadAndPlaySound(url)
        local SoundService = game:GetService("SoundService")
        local requestFunc = syn and syn.request or http_request or request
        if not requestFunc then
            warn("No HTTP request function available")
            return nil
        end
        local success, result = pcall(function()
            local response = requestFunc({
                Url = url,
                Method = "GET",
                Headers = {
                    ["User-Agent"] = "Roblox/Luau"
                }
            })
            if response and response.StatusCode == 200 then
                return response.Body
            else
                return nil
            end
        end)
        if success and result then
            local writeSuccess = pcall(function()
                writefile(".temp/audio.mp3", result)
            end)
            if writeSuccess then
                local sound = Instance.new("Sound")
                sound.SoundId = getcustomasset(".temp/audio.mp3")
                sound.Volume = 0.5
                sound.Parent = SoundService
                sound:Play()
                sound.Stopped:Connect(function()
                    sound:Destroy()
                    pcall(function() delfile(".temp/audio.mp3") end)
                end)
                return sound
            end
        end
        return nil
    end
    local function GetFinalSoundId(input)
        input = input:gsub("^%s*(.-)%s*$", "%1")
        if input:match("^%d+$") then
            return "rbxassetid://" .. input
        elseif input:match("^rbxasset://") or input:match("^rbxassetid://") then
            return input
        elseif input:match("^https?://") then
            return input
        else
            return nil
        end
    end
    local function ReplaceSound(sound, soundId)
        if IsRobloxAsset(soundId) then
            sound.SoundId = soundId
            return true
        elseif IsHttpAsset(soundId) then
            DownloadAndPlaySound(soundId)
            sound.Volume = 0
            sound:Stop()
            task.wait(0.1)
            sound:Destroy()
            return false
        end
        return false
    end
    local function replaceDeathSounds()
        for _, obj in ipairs(workspace:GetDescendants()) do
            if obj:IsA("Sound") then
                local name = obj.Name
                if name:lower():find("die") or name:lower():find("died") then
                    if IsRobloxAsset(replacementSoundId) then
                        obj.SoundId = replacementSoundId
                    elseif IsHttpAsset(replacementSoundId) then
                        DownloadAndPlaySound(replacementSoundId)
                        obj.Volume = 0
                        obj:Stop()
                        task.wait(0.1)
                        obj:Destroy()
                    end
                end
            end
        end
    end
    local function StopReplaceDeathSounds()
        if soundReplacementConnection then
            soundReplacementConnection:Disconnect()
            soundReplacementConnection = nil
        end
    end
    local function StartReplaceDeathSounds()
        if soundReplacementConnection then return end
        soundReplacementConnection = workspace.DescendantAdded:Connect(function(obj)
            if soundReplacementEnabled and obj:IsA("Sound") then
                local name = obj.Name
                if name:lower():find("die") or name:lower():find("died") then
                    if IsRobloxAsset(replacementSoundId) then
                        obj.SoundId = replacementSoundId
                    elseif IsHttpAsset(replacementSoundId) then
                        DownloadAndPlaySound(replacementSoundId)
                        obj.Volume = 0
                        obj:Stop()
                        task.wait(0.1)
                        obj:Destroy()
                    end
                end
            end
        end)
        replaceDeathSounds()
    end

    pcall(function() makefolder(".temp") end)

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

local secFastFlags = Tabs.Others:AddSection("Fastflags Injector", "solar/programming-bold")

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

    Tabs.Others:AddSpace({ Height = 20 })
    local SecAvatar = Tabs.Others:AddSection("Avatar", "solar/user-bold")
    SecAvatar:AddButton({
        Title = "Avatar Stealer",
        Callback = function() loadstring(game:HttpGet("https://darahub.pages.dev/api/script/Tools/avatar-stealer.lua"))() end
    })

    Tabs.Others:AddSpace({ Height = 20 })
    local SecDeathSounds = Tabs.Others:AddSection("Replace Death Sounds", "solar/volume-loud-bold")
    SecDeathSounds:AddDivider()
    SecDeathSounds:AddToggle("CustomDeathSoundsToggle", {
        Title = "Custom Death Sounds",
        Default = false,
        Callback = function(DConfiguration)
            soundReplacementEnabled = DConfiguration
            if DConfiguration then
                StartReplaceDeathSounds()
            else
                StopReplaceDeathSounds()
            end
        end
    })
    SecDeathSounds:AddInput("DeathSoundIdInput", {
        Title = "Sound ID or HTTP URL",
        Placeholder = "rbxasset://sounds/oof.ogg",
        Default = replacementSoundId,
        Callback = function(value)
            if value and value ~= "" then
                local finalId = GetFinalSoundId(value)
                if finalId then
                    replacementSoundId = finalId
                    if soundReplacementEnabled then
                        replaceDeathSounds()
                    end
                else
                    warn("Invalid input. Use: AssetID, rbxasset://, rbxassetid://, or http:// / https://")
                end
            end
        end
    })
    SecDeathSounds:AddButton({
        Title = "Test Sound",
        Callback = function()
            if IsRobloxAsset(replacementSoundId) then
                local SoundService = game:GetService("SoundService")
                local sound = Instance.new("Sound")
                sound.SoundId = replacementSoundId
                sound.Volume = 0.5
                sound.Parent = SoundService
                sound:Play()
                sound.Stopped:Connect(function() sound:Destroy() end)
            elseif IsHttpAsset(replacementSoundId) then
                DownloadAndPlaySound(replacementSoundId)
            end
        end
    })
    SecDeathSounds:AddButton({
        Title = "Apply to Existing Sounds",
        Callback = function()
            if soundReplacementEnabled then
                replaceDeathSounds()
            end
        end
    })

    Tabs.Others:AddSpace({ Height = 20 })
    local SecMapLoader = Tabs.Others:AddSection("Map Loader", "solar/global-bold")
    SecMapLoader:AddDivider()
    SecMapLoader:AddInput("MapAssetIdInput", {
        Title = "Map Asset ID",
        Placeholder = "Enter Map ID",
        Default = tostring(MAP_ASSET_ID),
        Callback = function(value)
            local num = tonumber(value)
            if num and num > 0 then
                MAP_ASSET_ID = num
            else
                warn("Invalid Map ID. Please enter a valid number.")
            end
        end
    })
    SecMapLoader:AddInput("MapInitialHeightInput", {
        Title = "Initial Height",
        Placeholder = "Enter height",
        Default = tostring(ALTURA_INICIAL),
        Callback = function(value)
            local num = tonumber(value)
            if num and num > 0 then
                ALTURA_INICIAL = num
            else
                warn("Invalid height. Please enter a valid number.")
            end
        end
    })
    local function LoadMapOnly()
        local root = GetRoot()
        if not root then return end
        local success, objs = pcall(function()
            return game:GetObjects("rbxassetid://" .. MAP_ASSET_ID)
        end)
        if success and objs[1] then
            local map = objs[1]
            map.Parent = workspace
            task.wait(1)
            local targetPart = map:FindFirstChildWhichIsA("BasePart", true)
            if targetPart then
                root.CFrame = targetPart.CFrame + Vector3.new(0, 5, 0)
            else
                root.CFrame = CFrame.new(0, 10, 0)
                warn("No BasePart found in map. Teleported to (0,10,0).")
            end
        else
            warn("Map loading failed. Please check if the ID is correct.")
        end
        root.Anchored = false
    end
    SecMapLoader:AddButton({
        Title = "Load Map",
        Callback = function()
            local root = GetRoot()
            if not root then return end
            root.Anchored = true
            root.CFrame = CFrame.new(root.Position.X, ALTURA_INICIAL, root.Position.Z)
            task.spawn(LoadMapOnly)
        end
    })
    SecMapLoader:AddButton({
        Title = "Reset Position",
        Callback = function()
            local root = GetRoot()
            if not root then return end
            root.Anchored = false
            root.CFrame = CFrame.new(0, 50, 0)
        end
    })

    Tabs.Others:AddSpace({ Height = 20 })
    local SecFixes = Tabs.Others:AddSection("Fixes", "solar/shield-check-bold")
    SecFixes:AddButton({
        Title = "Remove Game Pause UI",
        Callback = function()
            local success = false
            local networkPauseNotification = game:GetService("CoreGui"):FindFirstChild("RobloxGui")
            if networkPauseNotification then
                local modules = networkPauseNotification:FindFirstChild("Modules")
                local notification = modules and modules:FindFirstChild("NetworkPauseNotification")
                if notification then notification:Destroy() success = true end
                local networkPause = networkPauseNotification:FindFirstChild("CoreScripts/NetworkPause")
                if networkPause then networkPause:Destroy() success = true end
            end
        end
    })

    Tabs.Others:AddSpace({ Height = 20 })
    local SecDesync = Tabs.Others:AddSection("Desync", "solar/radar-bold")
    SecDesync:AddButton({
        Title = "Visualise Server Desync Position",
        Description = "Show how server side see your HRP, May not work when u turn off internet",
        Callback = function()
            local Players = game:GetService("Players")
            local RunService = game:GetService("RunService")
            local Stats = game:GetService("Stats")

            local dPlayer = Players.LocalPlayer
            local character = dPlayer.Character or dPlayer.CharacterAdded:Wait()
            local rootPart = character:WaitForChild("HumanoidRootPart")

            local UPDATE_INTERVAL = 0.05

            local fakeCharacter = Instance.new("Model")
            fakeCharacter.Name = "FakeCharacter_ServerView"
            fakeCharacter.Parent = workspace

            local fakeRootPart = Instance.new("Part")
            fakeRootPart.Name = "HumanoidRootPart"
            fakeRootPart.Size = rootPart.Size
            fakeRootPart.CFrame = rootPart.CFrame
            fakeRootPart.Anchored = true
            fakeRootPart.CanCollide = false
            fakeRootPart.Transparency = 1
            fakeRootPart.Material = Enum.Material.Plastic
            fakeRootPart.Parent = fakeCharacter

            local boxAdornment = Instance.new("BoxHandleAdornment")
            boxAdornment.Name = "ServerViewBox"
            boxAdornment.Adornee = fakeRootPart
            boxAdornment.Size = rootPart.Size + Vector3.new(0.5, 0.5, 0.5)
            boxAdornment.Color3 = Color3.new(1, 1, 1)
            boxAdornment.Transparency = 0.3
            boxAdornment.ZIndex = 0
            boxAdornment.AlwaysOnTop = true
            boxAdornment.Parent = fakeRootPart

            local fakeHumanoid = Instance.new("Humanoid")
            fakeHumanoid.Name = "Humanoid"
            fakeHumanoid.Parent = fakeCharacter

            local positionHistory = {}

            local function getRealPing()
                local ping = 0
                local pingSuccess, pingResult = pcall(function()
                    return Stats.Network.ServerStatsItem["Data Ping"]:GetValue()
                end)

                if pingSuccess and pingResult then
                    ping = pingResult
                end

                return ping
            end

            local function addPositionToHistory(position)
                local currentTime = tick()
                table.insert(positionHistory, {
                    position = position,
                    time = currentTime
                })

                local maxHistory = math.ceil(2000 / (UPDATE_INTERVAL * 1000)) + 10
                if #positionHistory > maxHistory then
                    table.remove(positionHistory, 1)
                end
            end

            local function getPositionAtTime(targetTime)
                if #positionHistory == 0 then
                    return rootPart.Position
                end

                local closestIndex = 1
                local closestDiff = math.abs(positionHistory[1].time - targetTime)

                for i = 2, #positionHistory do
                    local diff = math.abs(positionHistory[i].time - targetTime)
                    if diff < closestDiff then
                        closestDiff = diff
                        closestIndex = i
                    end
                end

                return positionHistory[closestIndex].position
            end

            local function updateFakeCharacter()
                if not character or not character.Parent then return end
                if not rootPart or not rootPart.Parent then return end

                local ping = getRealPing()
                ping = math.max(10, math.min(1000, ping))

                local pingDelay = ping / 1000

                addPositionToHistory(rootPart.Position)

                local serverTime = tick() - pingDelay

                local serverPosition
                if #positionHistory > 1 then
                    serverPosition = getPositionAtTime(serverTime)
                else
                    serverPosition = rootPart.Position
                end

                local currentCFrame = rootPart.CFrame
                local newCFrame = CFrame.new(serverPosition) * (currentCFrame - currentCFrame.Position)
                fakeRootPart.CFrame = newCFrame
            end

            local connection = RunService.Heartbeat:Connect(function()
                updateFakeCharacter()
            end)

            local function cleanup()
                if connection then
                    connection:Disconnect()
                    connection = nil
                end
                if fakeCharacter then
                    fakeCharacter:Destroy()
                end
            end

            dPlayer.CharacterAdded:Connect(function(newCharacter)
                cleanup()

                character = newCharacter
                rootPart = character:WaitForChild("HumanoidRootPart")

                positionHistory = {}

                fakeCharacter = Instance.new("Model")
                fakeCharacter.Name = "FakeCharacter_ServerView"
                fakeCharacter.Parent = workspace

                fakeRootPart = Instance.new("Part")
                fakeRootPart.Name = "HumanoidRootPart"
                fakeRootPart.Size = rootPart.Size
                fakeRootPart.CFrame = rootPart.CFrame
                fakeRootPart.Anchored = true
                fakeRootPart.CanCollide = false
                fakeRootPart.Transparency = 1
                fakeRootPart.Material = Enum.Material.Plastic
                fakeRootPart.Parent = fakeCharacter

                boxAdornment = Instance.new("BoxHandleAdornment")
                boxAdornment.Name = "ServerViewBox"
                boxAdornment.Adornee = fakeRootPart
                boxAdornment.Size = rootPart.Size + Vector3.new(0.5, 0.5, 0.5)
                boxAdornment.Color3 = Color3.new(1, 1, 1)
                boxAdornment.Transparency = 0.3
                boxAdornment.AlwaysOnTop = true
                boxAdornment.Parent = fakeRootPart

                fakeHumanoid = Instance.new("Humanoid")
                fakeHumanoid.Name = "Humanoid"
                fakeHumanoid.Parent = fakeCharacter

                connection = RunService.Heartbeat:Connect(function()
                    updateFakeCharacter()
                end)
            end)
        end
    })

    Tabs.Visuals:AddSpace({ Height = 20 })
    local SecExtraVisuals = Tabs.Visuals:AddSection("Extra", "solar/widget-2-bold")
    SecExtraVisuals:AddButton({
        Title = "Fake Headless",
        Callback = function()
            if getgenv().fakeheadless then return end
            local function applyHeadless(character)
                local head = character:WaitForChild("Head", 5)
                if head then
                    head.Transparency = 1
                    local face = head:FindFirstChild("face")
                    if face then face.Transparency = 1 end
                end
            end
            if player.Character then applyHeadless(player.Character) end
            player.CharacterAdded:Connect(applyHeadless)
            getgenv().fakeheadless = true
        end
    })
    SecExtraVisuals:AddButton({
        Title = "Fake Korblox",
        Callback = function()
            if getgenv().FakeKorblox then return end
            player.CharacterAdded:Connect(function(char)
                task.spawn(function()
                    while task.wait(0.5) do
                        if player.Character then
                            local hum = player.Character:FindFirstChildOfClass("Humanoid")
                            if hum then
                                if hum.RigType == Enum.HumanoidRigType.R15 then
                                    local ru = player.Character:FindFirstChild("RightUpperLeg")
                                    if ru then
                                        ru.MeshId = "rbxassetid://902942096"
                                        ru.TextureID = "rbxassetid://902843398"
                                        ru.Color = Color3.new(1, 1, 1)
                                        ru.Transparency = 0
                                    end
                                    local rf = player.Character:FindFirstChild("RightFoot")
                                    local rl = player.Character:FindFirstChild("RightLowerLeg")
                                    if rf and rl then
                                        rf.Transparency = 1
                                        rl.Transparency = 1
                                    end
                                else
                                    local rightLeg = player.Character:FindFirstChild("Right Leg")
                                    if rightLeg then
                                        for _, v in ipairs(player.Character:GetChildren()) do
                                            if v:IsA("CharacterMesh") and v.BodyPart == Enum.BodyPart.RightLeg then
                                                v:Destroy()
                                            end
                                        end
                                        local mesh = rightLeg:FindFirstChildOfClass("SpecialMesh") or Instance.new("SpecialMesh")
                                        mesh.Parent = rightLeg
                                        rightLeg.Color = Color3.fromRGB(64, 64, 64)
                                        rightLeg.Transparency = 0
                                        mesh.MeshType = Enum.MeshType.FileMesh
                                        mesh.MeshId = "rbxassetid://101851696"
                                        mesh.TextureId = "rbxassetid://101851254"
                                        mesh.Scale = Vector3.new(1, 1, 1)
                                    end
                                end
                            end
                        end
                    end
                end)
            end)
            getgenv().FakeKorblox = true
        end
    })
end
UniverseScriptsStuff(Tabs)

MediaManager:SetFolder("GoonWares/MurderMystery2/MediaCache")

InterfaceManager:SetLibrary(Fluent)
InterfaceManager:SetFolder("GoonWares/MurderMystery2")
InterfaceManager:BuildInterfaceSection(Tabs.Settings)
InterfaceManager:LoadSettings()

SaveManager:SetLibrary(Fluent)
SaveManager:SetFolder("GoonWares/MurderMystery2/Config")
SaveManager:IgnoreThemeSettings()
SaveManager:BuildConfigSection(Tabs.Settings)
SaveManager:LoadAutoloadConfig()

FloatingButtonManager:SetLibrary(Fluent)
FloatingButtonManager:SetFolder("GoonWares/MurderMystery2/Floating")
FloatingButtonManager:BuildConfigSection(Tabs.Settings)
FloatingButtonManager:LoadAutoloadConfig()

Notify("GoonWares", "All tabs loaded successfully", "Success", nil, 4)

task.delay(0.5, function()
    Window:SelectTab(1)
end)