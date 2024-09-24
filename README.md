-- StorageSTS

local correctGameId = 1022605215
local currentGameId = game.PlaceId

if currentGameId == correctGameId then

print("Survive The Slasher Script: Loading..")
print("Loaded!")

-- Load Rayfield library
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

-- Create the main window
local Window = Rayfield:CreateWindow({
    Name = "SURVIVE THE SLASHER HUB",
    LoadingTitle = "Survive The Slasher",
    LoadingSubtitle = "By Jor",
    ConfigurationSaving = {
        Enabled = true,
        FolderName = nil,
        FileName = "SURVIVE THE SLASHER HUB"
    },
    Discord = {
        Enabled = true,
        Invite = "H66dDfrS4Q",
        RememberJoins = false 
    },
    KeySystem = true,  -- Enable the key system
    KeySettings = {
        Title = "Key System",
        Subtitle = "Please Enter A Key To Access The Script!",
        Note = "The Key is in the discord",
        FileName = "Key",
        SaveKey = true,
        Key = {"NotterPlayjyfoxHACEKA#GSuslrrvi65753eSlsurvielslasherherKey689786549583"}  -- Set your desired key here
    }
})

-- Create tabs
local MainTab = Window:CreateTab("Local & Main", nil)
local SurviTab = Window:CreateTab("Survivor", nil)
local SlrTab = Window:CreateTab("Slasher", nil)
local ESTab = Window:CreateTab("ESP", nil)
local ExtrTab = Window:CreateTab("TP", nil)
local GameTab = Window:CreateTab("Game", nil)

-- Show a notification
Rayfield:Notify({
    Title = "Success!",
    Content = "Welcome!",
    Duration = 6.5,
    Image = nil,
    Actions = {
        Ignore = {
            Name = "Close",
            Callback = function() end
        }
    }
})

local infiniteJumpEnabled = false

game:GetService("UserInputService").JumpRequest:Connect(function()
    if infiniteJumpEnabled then
        game.Players.LocalPlayer.Character:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping")
    end
end)

MainTab:CreateButton({
    Name = "Rejoin",
    Callback = function()
    local TeleportService = game:GetService("TeleportService")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
 
local Rejoin = coroutine.create(function()
    local Success, ErrorMessage = pcall(function()
        TeleportService:Teleport(game.PlaceId, LocalPlayer)
    end)
 
    if ErrorMessage and not Success then
        warn(ErrorMessage)
    end
end)
 
coroutine.resume(Rejoin)
    end
})

MainTab:CreateSection("Local")

local infjToggle = false
local localPlayer = game.Players.LocalPlayer
local infiniteJumpEnabled = false -- Variable to control infinite jump

local infJump = MainTab:CreateToggle({
    Name = "Infinite Jump",
    Default = false,
    Callback = function(state)
        infjToggle = state
        infiniteJumpEnabled = infjToggle -- Set the infiniteJumpEnabled based on the toggle state
        end
})

local userInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local sprinting = false
local normalWalkSpeed = 16  -- Default walk speed
local sprintWalkSpeed = 32  -- Sprint speed
local defaultFOV = 70       -- Default camera FOV
local sprintFOV = 100       -- Camera FOV when sprinting
local tweenTime = 0.5       -- Time for the FOV transition
local inputBeganConnection, inputEndedConnection

-- Function to smoothly change FOV
local function tweenFOV(camera, targetFOV)
    local tweenInfo = TweenInfo.new(tweenTime, Enum.EasingStyle.Sine, Enum.EasingDirection.Out)
    local goal = {FieldOfView = targetFOV}
    local tween = TweenService:Create(camera, tweenInfo, goal)
    tween:Play()
end

-- Create the toggle
MainTab:CreateToggle({
    Name = "Shift To Sprint",
    Callback = function(state)
        -- Get player and character
        local player = game.Players.LocalPlayer
        local character = player.Character or player.CharacterAdded:Wait()
        local humanoid = character:WaitForChild("Humanoid")
        local camera = workspace.CurrentCamera

        if state then
            -- Start handling sprinting
            inputBeganConnection = userInputService.InputBegan:Connect(function(input, gameProcessedEvent)
                if not gameProcessedEvent and input.KeyCode == Enum.KeyCode.LeftShift then
                    humanoid.WalkSpeed = sprintWalkSpeed
                    tweenFOV(camera, sprintFOV)  -- Smooth FOV increase
                    sprinting = true
                end
            end)

            inputEndedConnection = userInputService.InputEnded:Connect(function(input)
                if input.KeyCode == Enum.KeyCode.LeftShift then
                    humanoid.WalkSpeed = normalWalkSpeed
                    tweenFOV(camera, defaultFOV)  -- Smooth FOV reset
                    sprinting = false
                end
            end)
        else
            -- Stop handling sprinting
            if inputBeganConnection then
                inputBeganConnection:Disconnect()
                inputBeganConnection = nil
            end
            if inputEndedConnection then
                inputEndedConnection:Disconnect()
                inputEndedConnection = nil
            end

            -- Reset walk speed and FOV if player is sprinting
            if sprinting then
                humanoid.WalkSpeed = normalWalkSpeed
                tweenFOV(camera, defaultFOV)  -- Smooth FOV reset
                sprinting = false
            end
        end
    end
})

-- Handle infinite jump functionality
game:GetService("UserInputService").JumpRequest:Connect(function()
    if infiniteJumpEnabled then
        local humanoid = localPlayer.Character and localPlayer.Character:FindFirstChildOfClass("Humanoid")
        if humanoid then
            humanoid:ChangeState(Enum.HumanoidStateType.Physics)
            humanoid:ChangeState(Enum.HumanoidStateType.Seated)
            humanoid:ChangeState(Enum.HumanoidStateType.GettingUp)
            humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
        end
    end
end)



local function resetTabs(tab)
    for _, button in ipairs(tab:GetChildren()) do
        if button:IsA("GuiButton") then
            button:Destroy()
        end
    end
end

local function handleSpeedChange(speed)
    local player = game.Players.LocalPlayer
    local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")

    if humanoid then
        if speed == 20 or speed == 22.5 then
            resetTabs(SurviTab)
            Rayfield:Notify({
                Title = "Warning",
                Content = "You cannot use Survivor scripts while being slasher!",
                Duration = 5,
                Image = nil
            })
        elseif speed == 16 then
            resetTabs(SlrTab)
            Rayfield:Notify({
                Title = "Warning",
                Content = "You cannot use Slasher scripts while being a survivor!",
                Duration = 5,
                Image = nil
            })
        end
    end
end

local walkSpeedSlider = MainTab:CreateSlider({
    Name = "WalkSpeed",
    Range = {16, 500},
    Increment = 1,
    Suffix = "Speed",
    CurrentValue = 16,
    Callback = function(Value)
        game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = Value
        handleSpeedChange(Value)
    end
})

local jumpPowerSlider = MainTab:CreateSlider({
    Name = "JumpPower",
    Range = {50, 550},
    Increment = 1,
    Suffix = "JumpPower",
    CurrentValue = 50,
    Callback = function(Value)
        game.Players.LocalPlayer.Character.Humanoid.JumpPower = Value
    end
})

MainTab:CreateButton({
    Name = "Unalive Yourself",
    Callback = function()
    game.Players.LocalPlayer.Character.Humanoid.MaxHealth = 0
    game.Players.LocalPlayer.Character.Humanoid.Health = 0
    end
})

MainTab:CreateButton({
    Name = "Reset Speed & JumpPower",
    Callback = function()
        game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = 16
        game.Players.LocalPlayer.Character.Humanoid.JumpPower = 50
        walkSpeedSlider:Set(16)
        jumpPowerSlider:Set(50)
    end
})

MainTab:CreateSection("Extra")

local antiKickConnection

MainTab:CreateToggle({
    Name = "Idle-Mode",
    Callback = function(state)
        if state then
            -- Activate Anti-Kick
            antiKickConnection = game:GetService("Players").LocalPlayer.Idled:Connect(function()
                local VirtualUser = game:GetService("VirtualUser")
                VirtualUser:Button2Down(Vector2.new(0, 0), workspace.CurrentCamera.CFrame)
                wait(1)
                VirtualUser:Button2Up(Vector2.new(0, 0), workspace.CurrentCamera.CFrame)
            end)
        else
            -- Deactivate Anti-Kick
            if antiKickConnection then
                antiKickConnection:Disconnect()
                antiKickConnection = nil
            end
        end
    end
})

MainTab:CreateParagraph({Title = "", Content = "Idle-Mode: Prevents You From Being Kicked Off The Game After 20 Minutes."}) 

MainTab:CreateButton({
    Name = "Infinite yield",
    Callback = function()
        loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))()
        end
})

MainTab:CreateToggle({
    Name = "Spy Private Messages",
    Default = false,
    Callback = function(value)
        enabled = value --chat "/spy" to toggle!
        spyOnMyself = true --if true will check your messages too
        public = false --if true will chat the logs publicly (fun, risky)
        publicItalics = true --if true will use /me to stand out
        privateProperties = { --customize private logs
	Color = Color3.fromRGB(0,255,255); 
	Font = Enum.Font.SourceSansBold;
	TextSize = 20;
}

        local StarterGui = game:GetService("StarterGui")
        local Players = game:GetService("Players")
        local player = Players.LocalPlayer or Players:GetPropertyChangedSignal("LocalPlayer"):Wait() or Players.LocalPlayer
        local saymsg = game:GetService("ReplicatedStorage"):WaitForChild("DefaultChatSystemChatEvents"):WaitForChild("SayMessageRequest")
        local getmsg = game:GetService("ReplicatedStorage"):WaitForChild("DefaultChatSystemChatEvents"):WaitForChild("OnMessageDoneFiltering")
        local instance = (_G.chatSpyInstance or 0) + 1
        _G.chatSpyInstance = instance

        local function onChatted(p, msg)
            if _G.chatSpyInstance == instance then
                if p == player and msg:lower():sub(1, 4) == "/spy" then
                    enabled = not enabled
                    wait(0.3)
                    privateProperties.Text = "[SPY " .. (enabled and "EN" or "DIS") .. "ABLED]"
                    StarterGui:SetCore("ChatMakeSystemMessage", privateProperties)
                elseif enabled and (spyOnMyself == true or p ~= player) then
                    msg = msg:gsub("[\n\r]", ''):gsub("\t", ' '):gsub("[ ]+", ' ')
                    local hidden = true
                    local conn = getmsg.OnClientEvent:Connect(function(packet, channel)
                        if packet.SpeakerUserId == p.UserId and packet.Message == msg:sub(#msg - #packet.Message + 1) and (channel == "All" or (channel == "Team" and public == false and Players[packet.FromSpeaker].Team == player.Team)) then
                            hidden = false
                        end
                    end)
                    wait(1)
                    conn:Disconnect()
                    if hidden and enabled then
                        if public then
                            saymsg:FireServer((publicItalics and "/me " or '') .. "[SPY] [" .. p.Name .. "]: " .. msg, "All")
                        else
                            privateProperties.Text = "[SPY] [" .. p.Name .. "]: " .. msg
                            StarterGui:SetCore("ChatMakeSystemMessage", privateProperties)
                        end
                    end
                end
            end
        end

        for _, p in ipairs(Players:GetPlayers()) do
            p.Chatted:Connect(function(msg) onChatted(p, msg) end)
        end
        Players.PlayerAdded:Connect(function(p)
            p.Chatted:Connect(function(msg) onChatted(p, msg) end)
        end)
        privateProperties.Text = "[SPY " .. (enabled and "EN" or "DIS") .. "ABLED]"
        StarterGui:SetCore("ChatMakeSystemMessage", privateProperties)
        if not player.PlayerGui:FindFirstChild("Chat") then wait(3) end
        local chatFrame = player.PlayerGui.Chat.Frame
        chatFrame.ChatChannelParentFrame.Visible = true
        chatFrame.ChatBarParentFrame.Position = chatFrame.ChatChannelParentFrame.Position + UDim2.new(0, 0, chatFrame.ChatChannelParentFrame.Size.Y.Scale, chatFrame.ChatChannelParentFrame.Size.Y.Offset)
    end
})

MainTab:CreateSection("Protection")

local staffProtectionToggle = false
local staffProtectionConnection
local localPlayer = game.Players.LocalPlayer

-- Main toggle for staff protection
MainTab:CreateToggle({
    Name = "Kick If Admin Joins",
    CurrentValue = false,
    Callback = function(state)
        staffProtectionToggle = state

        local targetUserIDs = {
            [362956857] = true,
            [28724905] = true,
            [707723783] = true,
            [331273890] = true,
            [4791037402] = true,
            [311314197] = true,
            [2241157448] = true,
            [3524879643] = true,
            [862638016] = true,
            [316330352] = true,
            [2977642950] = true,
            [3120444159] = true,
            [3047319330] = true
        }

        local function kickPlayerIfNeeded(joinedPlayer)
            if targetUserIDs[joinedPlayer.UserId] then
                localPlayer:Kick("An Admin From This Game Has Joined Your Server, You Have Been Kicked For Your Safety.")
            end
        end

        if state then
            -- Check existing players
            for _, existingPlayer in ipairs(game.Players:GetPlayers()) do
                kickPlayerIfNeeded(existingPlayer)
            end

            -- Check new players
            staffProtectionConnection = game.Players.PlayerAdded:Connect(kickPlayerIfNeeded)
        else
            if staffProtectionConnection then
                staffProtectionConnection:Disconnect()
                staffProtectionConnection = nil
            end
        end
    end
})

MainTab:CreateParagraph({Title = "", Content = "Kick If Admin Joins: Automatically Kicks You If Admin Joins."})

local staffProtectionToggle = false
local staffProtectionConnection
local localPlayer = game.Players.LocalPlayer

-- Main toggle for staff protection
MainTab:CreateToggle({
    Name = "Kick If Admin Joins 2",
    CurrentValue = false,
    Callback = function(state)
        staffProtectionToggle = state

        local targetUserIDs = {
            [362956857] = true,
            [28724905] = true,
            [707723783] = true,
            [331273890] = true,
            [4791037402] = true,
            [311314197] = true,
            [2241157448] = true,
            [3524879643] = true,
            [862638016] = true,
            [316330352] = true,
            [2977642950] = true,
            [3120444159] = true,
            [3047319330] = true
        }

        local function handlePlayer(joinedPlayer)
            if targetUserIDs[joinedPlayer.UserId] then
                -- Show a notification with Yes and No buttons
                Rayfield:Notify({
                    Title = "Admin Joined Alert!",
                    Content = "An Admin has joined your game. Do you want to leave?",
                    Duration = 3600,
                    Image = nil,
                    Actions = {
                        Yes = {
                            Name = "Yes",
                            Callback = function()
                                localPlayer:Kick("An Admin From This Game Has Joined Your Server, You Choose To Be Kicked For Your Safety.")
                            end
                        },
                        No = {
                            Name = "No",
                            Callback = function()
                            end
                        }
                    }
                })
            end
        end

        if state then
            -- Check existing players
            for _, existingPlayer in ipairs(game.Players:GetPlayers()) do
                handlePlayer(existingPlayer)
            end

            -- Check new players
            staffProtectionConnection = game.Players.PlayerAdded:Connect(handlePlayer)
        else
            if staffProtectionConnection then
                staffProtectionConnection:Disconnect()
                staffProtectionConnection = nil
            end
        end
    end
})

MainTab:CreateParagraph({Title = "", Content = "Kick If Admin Joins 2: Alerts You And Gives Option To Leave If Admin Joins."})

ESTab:CreateSection("ESP")

local espToggle = false
local espConnection

ESTab:CreateToggle({
    Name = "ESP All",
    CurrentValue = false,
    Callback = function(state)
        local Players = game:GetService("Players")
        local player = Players.LocalPlayer

        espToggle = state

        if espToggle then
            local RunService = game:GetService("RunService")
            local Camera = game:GetService("Workspace").CurrentCamera
            local LocalPlayer = Players.LocalPlayer

            local function createOutline(player)
                local character = player.Character
                if character and character:FindFirstChild("HumanoidRootPart") then
                    local highlight = Instance.new("Highlight", character)
                    highlight.Name = "ESP"
                    highlight.Adornee = character
                    highlight.FillTransparency = 0.3
                    highlight.OutlineTransparency = 0
                    highlight.OutlineColor = Color3.fromRGB(0, 0, 0) -- Black outline
                end
            end

            local function updateESP()
                for _, player in pairs(Players:GetPlayers()) do
                    -- Ensure player and character exist
                    if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Humanoid") then
                        local humanoid = player.Character.Humanoid
                        local walkSpeed = humanoid.WalkSpeed
                        local esp = player.Character:FindFirstChild("ESP")

                        -- If ESP does not exist, create it
                        if not esp then
                            createOutline(player)
                            esp = player.Character:FindFirstChild("ESP")
                        end

                        -- Check if 'esp' was successfully created before modifying it
                        if esp and esp:IsA("Highlight") then
                            local color
                            if walkSpeed == 16 then
                                color = Color3.fromRGB(0,255,127)
                            elseif walkSpeed == 5 then
                                color = Color3.fromRGB(204, 204, 0)
                            elseif walkSpeed == 20 or walkSpeed == 22.5 then
                                color = Color3.fromRGB(255, 0, 0)
                            elseif walkSpeed == 24 then
                                color = Color3.fromRGB(0,255,127)
                            elseif walkSpeed == 25 then
                                color = Color3.fromRGB(172, 79, 198)
                            else
                                color = Color3.fromRGB(252, 76, 2)
                            end
                            esp.FillColor = color -- Adjust fill color based on walk speed
                        end
                    end
                end
            end

            espConnection = RunService.Heartbeat:Connect(updateESP)
        else
            if espConnection then
                espConnection:Disconnect()
            end

            -- Remove all ESP elements (only Highlight)
            for _, player in pairs(Players:GetPlayers()) do
                if player.Character then
                    local espOutline = player.Character:FindFirstChild("ESP")
                    if espOutline and espOutline:IsA("Highlight") then
                        espOutline:Destroy()
                    end
                end
            end
        end
    end
})

local espToggle = false
local espConnection
local color = Color3.fromRGB(255, 0, 0) -- Default color for ESP

ESTab:CreateToggle({
    Name = "ESP Slasher",
    CurrentValue = false,
    Callback = function(state)
        local Players = game:GetService("Players")
        local player = Players.LocalPlayer

        espToggle = state

        if espToggle then
            local RunService = game:GetService("RunService")
            local LocalPlayer = Players.LocalPlayer

            local function createOutline(player)
                local character = player.Character
                if character and character:FindFirstChild("HumanoidRootPart") then
                    local highlight = Instance.new("Highlight")
                    highlight.Name = "ESP"
                    highlight.Adornee = character
                    highlight.FillTransparency = 0.3
                    highlight.OutlineTransparency = 0
                    highlight.OutlineColor = Color3.fromRGB(0, 0, 0) -- Black outline
                    highlight.Parent = character
                end
            end

            local function updateESP()
                for _, player in pairs(Players:GetPlayers()) do
                    if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Humanoid") then
                        local humanoid = player.Character.Humanoid
                        local walkSpeed = humanoid.WalkSpeed

                        if walkSpeed == 20 or walkSpeed == 22.5 then
                            local esp = player.Character:FindFirstChild("ESP")
                            if not esp then
                                createOutline(player)
                            end
                            local highlight = player.Character:FindFirstChild("ESP")
                            if highlight and highlight:IsA("Highlight") then
                                highlight.FillColor = color
                            end
                        end
                    end
                end
            end

            espConnection = RunService.Heartbeat:Connect(updateESP)
        else
            if espConnection then
                espConnection:Disconnect()
            end

            -- Remove all ESP elements (Highlight only)
            for _, player in pairs(Players:GetPlayers()) do
                if player.Character then
                    local highlight = player.Character:FindFirstChild("ESP")
                    if highlight and highlight:IsA("Highlight") then
                        highlight:Destroy()
                    end
                end
            end
        end
    end
})

local espToggle = false
local espConnection

ESTab:CreateToggle({
    Name = "ESP Players",
    CurrentValue = false,
    Callback = function(state)
        local Players = game:GetService("Players")
        local player = Players.LocalPlayer

        espToggle = state

        if espToggle then
            local RunService = game:GetService("RunService")
            local LocalPlayer = Players.LocalPlayer

            local function createOutline(player)
                local character = player.Character
                if character and character:FindFirstChild("HumanoidRootPart") then
                    local highlight = Instance.new("Highlight", character)
                    highlight.Name = "ESP"
                    highlight.Adornee = character
                    highlight.FillTransparency = 0.3
                    highlight.OutlineTransparency = 0
                    highlight.OutlineColor = Color3.fromRGB(0, 0, 0)
                end
            end

            local function updateESP()
                for _, player in pairs(Players:GetPlayers()) do
                    if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Humanoid") then
                        local humanoid = player.Character.Humanoid
                        local walkSpeed = humanoid.WalkSpeed
                        local esp = player.Character:FindFirstChild("ESP")

                        if walkSpeed ~= 20 and walkSpeed ~= 22.5 then
                            if not esp then
                                createOutline(player)
                            end

                            local color
                            if walkSpeed == 16 then
                                color = Color3.fromRGB(0,255,127) -- Green for 16
                            elseif walkSpeed == 5 then
                                color = Color3.fromRGB(204, 204, 0) -- Yellow for 5
                            elseif walkSpeed == 24 then
                                color = Color3.fromRGB(0,255,127) -- Green for 24
                            elseif walkSpeed == 25 then
                                color = Color3.fromRGB(172, 79, 198) -- Purple for 25
                            end

                            if color then
                                if esp and esp:IsA("Highlight") then
                                    esp.FillColor = color
                                end
                            end
                        else
                            if esp then
                                esp:Destroy() -- Remove ESP for players with 20 or 22.5 walk speed
                            end
                        end
                    end
                end
            end

            espConnection = RunService.Heartbeat:Connect(updateESP)
        else
            if espConnection then
                espConnection:Disconnect()
            end

            for _, player in pairs(Players:GetPlayers()) do
                if player.Character then
                    local espOutline = player.Character:FindFirstChild("ESP")
                    if espOutline and espOutline:IsA("Highlight") then
                        espOutline:Destroy()
                    end
                end
            end
        end
    end
})

local espToggle = false 
local espConnection

ESTab:CreateToggle({
    Name = "ESP Coins",
    CurrentValue = false,
    Callback = function(state)
        espToggle = state

        if espToggle then
            local RunService = game:GetService("RunService")
            local Workspace = game:GetService("Workspace")
            local coinModel = Workspace:WaitForChild("CurrentMap"):WaitForChild("Coins")

            local neonColor = Color3.fromRGB(212, 175, 55) -- Neon golden color

            local function createNeonBox(part)
                if part and part:IsA("BasePart") then
                    local box = Instance.new("BoxHandleAdornment")
                    box.Name = "CoinESPBox"
                    box.Adornee = part
                    box.Color3 = neonColor
                    box.Transparency = 0.2 -- More solid for a neon look
                    box.Size = part.Size + Vector3.new(0.1, 0.1, 0.1)
                    box.AlwaysOnTop = true
                    box.ZIndex = 10
                    box.Parent = part

                    -- Add a neon glow effect
                    local neonEffect = Instance.new("SelectionBox")
                    neonEffect.Adornee = part
                    neonEffect.LineThickness = 0.05
                    neonEffect.SurfaceColor3 = neonColor
                    neonEffect.SurfaceTransparency = 0.7
                    neonEffect.Color3 = neonColor
                    neonEffect.Parent = part
                end
            end

            local function updateESP()
                for _, part in pairs(coinModel:GetChildren()) do
                    if part:IsA("BasePart") and not part:FindFirstChild("CoinESPBox") then
                        createNeonBox(part)
                    end
                end
            end

            espConnection = RunService.Heartbeat:Connect(updateESP)
        else
            if espConnection then
                espConnection:Disconnect()
            end

            -- Remove all ESP elements (BoxHandleAdornment and SelectionBox)
            local coinModel = game:GetService("Workspace"):WaitForChild("CurrentMap"):WaitForChild("Coins")
            for _, part in pairs(coinModel:GetChildren()) do
                local espBox = part:FindFirstChild("CoinESPBox")
                if espBox then
                    espBox:Destroy()
                end
                local neonEffect = part:FindFirstChildOfClass("SelectionBox")
                if neonEffect then
                    neonEffect:Destroy()
                end
            end
        end
    end
})

SurviTab:CreateSection("Survivor Perks")

local seslreek = SurviTab:CreateButton({
    Name = "Spectate Slasher",
    Callback = function()
        local Players = game:GetService("Players")
        local player = Players.LocalPlayer
        local camera = workspace.CurrentCamera
        local tweenService = game:GetService("TweenService")

        -- Function to create and display a styled text label
        local function createStyledTextLabel(text, color)
            local screenGui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
            local textLabel = Instance.new("TextLabel", screenGui)
            textLabel.Size = UDim2.new(0, 600, 0, 100)
            textLabel.Position = UDim2.new(0.5, -300, -0.1, 0)  -- Start above the screen
            textLabel.Text = text
            textLabel.TextScaled = true
            textLabel.BackgroundTransparency = 1
            textLabel.TextColor3 = color
            textLabel.Font = Enum.Font.GothamBlack
            textLabel.TextStrokeTransparency = 0
            textLabel.TextStrokeColor3 = Color3.fromRGB(0, 0, 0) -- Black stroke

            -- Slide-in effect
            local targetPosition = UDim2.new(0.5, -300, 0.5, -50) -- Centered position
            local tweenInfo = TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
            local slideInTween = tweenService:Create(textLabel, tweenInfo, {Position = targetPosition})
            slideInTween:Play()

            -- Smooth fade-out after a delay
            slideInTween.Completed:Connect(function()
                task.delay(4, function()
                    local fadeOutTween = tweenService:Create(textLabel, TweenInfo.new(1, Enum.EasingStyle.Quad, Enum.EasingDirection.In), {TextTransparency = 1, TextStrokeTransparency = 1})
                    fadeOutTween:Play()
                    fadeOutTween.Completed:Connect(function()
                        textLabel:Destroy()
                    end)
                end)
            end)

            return textLabel
        end

        -- Function to check if there are players with specified walk speeds
        local function hasPlayersWithWalkSpeed(speeds)
            for _, p in ipairs(Players:GetPlayers()) do
                if p.Character and p.Character:FindFirstChild("Humanoid") then
                    local humanoid = p.Character:FindFirstChild("Humanoid")
                    if table.find(speeds, humanoid.WalkSpeed) then
                        return true
                    end
                end
            end
            return false
        end

        -- Function to move the camera to a player with specified walk speeds
        local function moveToPlayerWithWalkSpeed(speeds)
            for _, p in ipairs(Players:GetPlayers()) do
                if p.Character and p.Character:FindFirstChild("Humanoid") then
                    local humanoid = p.Character:FindFirstChild("Humanoid")
                    if table.find(speeds, humanoid.WalkSpeed) then
                        camera.CameraSubject = p.Character
                        camera.CameraType = Enum.CameraType.Attach
                        return
                    end
                end
            end
        end

        -- Function to create a GUI button with a background
        local function createButton()
            local screenGui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
            local button = Instance.new("TextButton", screenGui)
            local uiCorner = Instance.new("UICorner", button)
            
            button.Size = UDim2.new(0, 250, 0, 75) -- Increased size
            button.Position = UDim2.new(0.5, -125, 0.8, 0) -- Adjusted position for new size
            button.Text = "EXIT"
            button.BackgroundColor3 = Color3.fromRGB(255, 0, 0) -- Red background
            button.TextColor3 = Color3.fromRGB(255, 255, 255) -- White text
            button.Font = Enum.Font.GothamBlack
            button.TextScaled = true
            button.TextStrokeTransparency = 0
            button.TextStrokeColor3 = Color3.fromRGB(0, 0, 0) -- Black stroke

            -- Add rounded corners with a radius of 0.15
            uiCorner.CornerRadius = UDim.new(0, 15)

            -- Function to move the camera back to normal view
            button.MouseButton1Click:Connect(function()
                camera.CameraSubject = player.Character.HumanoidRootPart
                camera.CameraType = Enum.CameraType.Custom
                screenGui:Destroy()
            end)
        end

        -- Check for players with the specified walk speeds before proceeding
        if hasPlayersWithWalkSpeed({20, 22.5}) then
            -- Call the function to move the camera
            moveToPlayerWithWalkSpeed({20, 22.5})
            
            -- Call the function to create the button
            createButton()
        else
            -- Show warning message if no player with specified walk speed is found
            createStyledTextLabel("Warning: Please wait until Slasher is selected or spawns to use this.", Color3.fromRGB(255, 174, 66))
        end
    end,
})

SurviTab:CreateButton({
    Name = "Heal Me",
    Callback = function()
        local tweenService = game:GetService("TweenService")
        local player = game.Players.LocalPlayer
        local character = player.Character or player.CharacterAdded:Wait()
        local humanoid = character:WaitForChild("Humanoid")
        local humanoidRootPart = character:WaitForChild("HumanoidRootPart")
        local backpack = player:WaitForChild("Backpack")
        local originalPosition = humanoidRootPart.Position
        local targetPlayer = nil
        local Players = game:GetService("Players")
        local teleporting = true

        -- Create a styled text label
        local function createStyledTextLabel(text, color)
            local screenGui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
            local textLabel = Instance.new("TextLabel", screenGui)
            textLabel.Size = UDim2.new(0, 600, 0, 100)
            textLabel.Position = UDim2.new(0.5, -300, -0.1, 0)  -- Start above the screen
            textLabel.Text = text
            textLabel.TextScaled = true
            textLabel.BackgroundTransparency = 1
            textLabel.TextColor3 = color
            textLabel.Font = Enum.Font.GothamBlack
            textLabel.TextStrokeTransparency = 0
            textLabel.TextStrokeColor3 = Color3.fromRGB(0, 0, 0) -- Black stroke

            -- Slide-in effect
            local targetPosition = UDim2.new(0.5, -300, 0.5, -50) -- Centered position
            local tweenInfo = TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
            local slideInTween = tweenService:Create(textLabel, tweenInfo, {Position = targetPosition})
            slideInTween:Play()

            -- Smooth fade-out after a delay
            slideInTween.Completed:Connect(function()
                task.delay(4, function()
                    local fadeOutTween = tweenService:Create(textLabel, TweenInfo.new(1, Enum.EasingStyle.Quad, Enum.EasingDirection.In), {TextTransparency = 1, TextStrokeTransparency = 1})
                    fadeOutTween:Play()
                    fadeOutTween.Completed:Connect(function()
                        textLabel:Destroy()
                    end)
                end)
            end)

            return textLabel
        end

        -- Check for DisplayGun and Speed Shake
        local function checkConditions()
            if character:FindFirstChild("DisplayGun") then
                createStyledTextLabel("Warning: You can't use this in the lobby.", Color3.fromRGB(255, 174, 66))
                return false
            elseif (humanoid.WalkSpeed == 20 or humanoid.WalkSpeed == 22.5) and 
                (not backpack:FindFirstChild("Speed Shake") and not character:FindFirstChild("Speed Shake")) then
                createStyledTextLabel("Warning: You can't use this while you are slasher.", Color3.fromRGB(255, 174, 66))
                return false
            elseif humanoid.WalkSpeed == 16 then
                createStyledTextLabel("Warning: You must be injured by the slasher to use this.", Color3.fromRGB(255, 174, 66))
                return false
            end
            return true
        end

        -- Function to find a target player with WalkSpeed of 20 or 22.5
        local function findTargetPlayer()
            for _, otherPlayer in pairs(Players:GetPlayers()) do
                if otherPlayer ~= player and otherPlayer.Character then
                    local otherHumanoid = otherPlayer.Character:FindFirstChildOfClass("Humanoid")
                    if otherHumanoid and (otherHumanoid.WalkSpeed == 20 or otherHumanoid.WalkSpeed == 22.5) then
                        return otherPlayer
                    end
                end
            end
            return nil
        end

        -- Function to teleport to the target player
        local function teleportToTarget()
            if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
                humanoidRootPart.CFrame = targetPlayer.Character.HumanoidRootPart.CFrame
            end
        end

        -- Function to spam teleport and handle ForceField check
        local function spamTeleport()
            targetPlayer = findTargetPlayer()

            -- Spam teleport to player with WalkSpeed 20 or 22.5
            while teleporting do
                if targetPlayer then
                    teleportToTarget()
                else
                    targetPlayer = findTargetPlayer()
                end
                
                -- Check for ForceField
                if character:FindFirstChild("ForceField") then
                    teleporting = false
                    humanoidRootPart.CFrame = CFrame.new(originalPosition) -- Teleport back to original position
                    break
                end
                wait(0.05)
            end
        end

        -- Check the conditions before teleporting
        if checkConditions() then
            spamTeleport()
        end
    end
})

SurviTab:CreateButton({
    Name = "Kill Slasher (ONLY BEAST MODE)",
    Callback = function()
        local player = game.Players.LocalPlayer
        local mouse = player:GetMouse()
        
        -- Function to get the walkspeed of a target
        local function getWalkSpeed(target)
            if target and target:FindFirstChild("Humanoid") then
                return target.Humanoid.WalkSpeed
            end
            return nil
        end

        -- Function to teleport behind the target
        local function teleportBehind(target)
            local distance = 5 -- Medium distance behind
            local behindPosition = target.Position - (target.CFrame.LookVector * distance)
            player.Character.HumanoidRootPart.CFrame = CFrame.new(behindPosition)
        end

        -- Function to simulate clicks
        local function simulateClick()
            mouse1click()
        end

        -- Find a player with the correct walkspeed
        local function findTarget()
            for _, targetPlayer in pairs(game.Players:GetPlayers()) do
                if targetPlayer ~= player and targetPlayer.Character then
                    local walkspeed = getWalkSpeed(targetPlayer.Character)
                    if walkspeed == 20 or walkspeed == 22.5 then
                        return targetPlayer
                    end
                end
            end
            return nil
        end

        -- Main loop until the target is dead
        while true do
            local targetPlayer = findTarget()
            if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("Humanoid") then
                local target = targetPlayer.Character.HumanoidRootPart
                teleportBehind(target) -- Teleport behind target
                mouse.Hit = target.CFrame -- Aim at the target
                simulateClick() -- Simulate clicking
                
                -- Check target health to stop when dead
                if targetPlayer.Character.Humanoid.Health <= 0 then
                    break
                end
            end
            wait(0.1) -- Delay to avoid instant execution
        end
    end
})

local toggle = false
local connection
local PseudoAnchor
local CanInvis = true
local IsInvisible = false
local FakeCharacter
local Part
local Player = game.Players.LocalPlayer
local RealCharacter = Player.Character or Player.CharacterAdded:Wait()
local humanoid = RealCharacter:WaitForChild("Humanoid")
local Transparency = true
local NoClip = false
local LightPart -- Part for light to indicate player's position
local FaceDecal
local tweenService = game:GetService("TweenService")
local originalTransparency = {}

SurviTab:CreateToggle({
    Name = "Invisible (Disables Game Interaction)",
    CurrentValue = false,
    Flag = "SlasherToggle",
    Callback = function(Value)
        toggle = Value

        local function createStyledTextLabel(text, color)
            local screenGui = Instance.new("ScreenGui", Player:WaitForChild("PlayerGui"))
            local textLabel = Instance.new("TextLabel", screenGui)
            textLabel.Size = UDim2.new(0, 600, 0, 100)
            textLabel.Position = UDim2.new(0.5, -300, 0.1, 0)
            textLabel.Text = text
            textLabel.TextScaled = true
            textLabel.BackgroundTransparency = 1
            textLabel.TextColor3 = color
            textLabel.Font = Enum.Font.GothamBlack
            textLabel.TextStrokeTransparency = 0
            textLabel.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)

            local targetPosition = UDim2.new(0.5, -300, 0.5, -50)
            local tweenInfo = TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
            local slideInTween = tweenService:Create(textLabel, tweenInfo, {Position = targetPosition})
            slideInTween:Play()

            slideInTween.Completed:Connect(function()
                task.delay(4, function()
                    local fadeOutTween = tweenService:Create(textLabel, TweenInfo.new(1, Enum.EasingStyle.Quad, Enum.EasingDirection.In), {TextTransparency = 1, TextStrokeTransparency = 1})
                    fadeOutTween:Play()
                    fadeOutTween.Completed:Connect(function()
                        textLabel:Destroy()
                    end)
                end)
            end)
        end

        -- Ensure invisibility can still proceed
        if RealCharacter:FindFirstChild("DisplayGun") then
            createStyledTextLabel("Warning: You can't become invisible in the lobby, wait until game starts.", Color3.fromRGB(255, 174, 66))
            return
        elseif humanoid.WalkSpeed == 20 or humanoid.WalkSpeed == 22.5 then
            createStyledTextLabel("Warning: You can't become invisible while being slasher, it will glitch.", Color3.fromRGB(255, 174, 66))
            return
        end

        -- Initialize the fake character
        local function setupFakeCharacter()
            RealCharacter.Archivable = true
            FakeCharacter = RealCharacter:Clone()

            Part = Instance.new("Part", workspace)
            Part.Anchored = true
            Part.Size = Vector3.new(200, 1, 200)
            Part.CFrame = CFrame.new(0, -500, 0)
            Part.CanCollide = true

            LightPart = Instance.new("Part", workspace)
            LightPart.Anchored = true
            LightPart.CanCollide = false
            LightPart.Shape = Enum.PartType.Ball
            LightPart.Size = Vector3.new(5, 5, 5)
            LightPart.CFrame = RealCharacter.HumanoidRootPart.CFrame
            local light = Instance.new("PointLight", LightPart)
            light.Brightness = 2
            light.Range = 10

            FakeCharacter.Parent = workspace
            FakeCharacter.HumanoidRootPart.CFrame = Part.CFrame * CFrame.new(0, 5, 0)

            if Transparency then
                for _, v in pairs(FakeCharacter:GetDescendants()) do
                    if v:IsA("BasePart") then
                        v.Transparency = 1
                    elseif v:IsA("Decal") and v.Name == "face" then
                        v.Transparency = 1 -- Hide face decal
                    end
                end
            end

            for _, v in pairs(RealCharacter:GetChildren()) do
                if v:IsA("LocalScript") then
                    local clone = v:Clone()
                    clone.Disabled = true
                    clone.Parent = FakeCharacter
                end
            end

            PseudoAnchor = FakeCharacter.HumanoidRootPart
        end

        -- Toggle invisibility
        local function Invisible(enable)
            if enable and not IsInvisible then
                local StoredCF = RealCharacter.HumanoidRootPart.CFrame
                RealCharacter.HumanoidRootPart.CFrame = FakeCharacter.HumanoidRootPart.CFrame
                FakeCharacter.HumanoidRootPart.CFrame = StoredCF
                RealCharacter.Humanoid:UnequipTools()
                Player.Character = FakeCharacter
                workspace.CurrentCamera.CameraSubject = FakeCharacter.Humanoid
                PseudoAnchor = RealCharacter.HumanoidRootPart

                for _, v in pairs(FakeCharacter:GetChildren()) do
                    if v:IsA("LocalScript") then
                        v.Disabled = false
                    end
                end

                -- Hide real character's face decal
                for _, v in pairs(RealCharacter:GetChildren()) do
                    if v:IsA("Decal") and v.Name == "face" then
                        FaceDecal = v
                        v.Transparency = 1
                    end
                end

                IsInvisible = true
            elseif not enable and IsInvisible then
                local StoredCF = FakeCharacter.HumanoidRootPart.CFrame
                FakeCharacter.HumanoidRootPart.CFrame = RealCharacter.HumanoidRootPart.CFrame
                RealCharacter.HumanoidRootPart.CFrame = StoredCF

                FakeCharacter.Humanoid:UnequipTools()
                Player.Character = RealCharacter
                workspace.CurrentCamera.CameraSubject = RealCharacter.Humanoid
                PseudoAnchor = FakeCharacter.HumanoidRootPart

                for _, v in pairs(FakeCharacter:GetChildren()) do
                    if v:IsA("LocalScript") then
                        v.Disabled = true
                    end
                end

                -- Restore real character's face decal
                if FaceDecal then
                    FaceDecal.Transparency = 0
                end

                IsInvisible = false
            end
        end

        if toggle then
            if not FakeCharacter then
                setupFakeCharacter()
            end
            Invisible(true) -- Enable invisibility

            connection = game:GetService("RunService").RenderStepped:Connect(function()
                if NoClip and FakeCharacter then
                    FakeCharacter.Humanoid:ChangeState(11) -- NoClip mode
                end
                if PseudoAnchor then
                    PseudoAnchor.CFrame = Part.CFrame * CFrame.new(0, 5, 0) -- Keep the fake character in place
                end

                if LightPart and RealCharacter and RealCharacter:FindFirstChild("HumanoidRootPart") then
                    LightPart.CFrame = RealCharacter.HumanoidRootPart.CFrame * CFrame.new(0, 5, 0)
                end
            end)
        else
            if IsInvisible then
                Invisible(false) -- Disable invisibility
            end
            if FakeCharacter then
                FakeCharacter:Destroy()
                FakeCharacter = nil
            end
            if Part then
                Part:Destroy()
                Part = nil
            end
            if LightPart then
                LightPart:Destroy()
                LightPart = nil
            end
            if connection then
                connection:Disconnect()
                connection = nil
            end
        end
    end
})

SlrTab:CreateSection("Slasher Perks")

SlrTab:CreateButton({
    Name = "Click For More!",
    Callback = function()
loadstring(game:HttpGet("https://raw.githubusercontent.com/ProjectpopCat/ywxoscripts/main/STS.lua"))()
    end
})

ExtrTab:CreateSection("TP")

ExtrTab:CreateButton({
    Name = "Teleport To Lobby",
    Callback = function()
        local player = game.Players.LocalPlayer
        local character = player.Character
        local lobbySpawns = game.Workspace:FindFirstChild("Lobby"):FindFirstChild("LobbySpawns")

        if lobbySpawns and character then
            local spawnPoints = lobbySpawns:GetChildren()
            local randomSpawn = spawnPoints[math.random(1, #spawnPoints)]
            if randomSpawn:IsA("BasePart") then
                character:MoveTo(randomSpawn.Position)
            end
        end
    end
})

ExtrTab:CreateButton({
    Name = "Teleport To Current Map",
    Callback = function()
        local player = game.Players.LocalPlayer
        local character = player.Character
        local survivorSpawns = game.Workspace:FindFirstChild("CurrentMap"):FindFirstChild("SurvivorSpawns")

        if survivorSpawns and character then
            local spawnPoints = survivorSpawns:GetChildren()
            local randomSpawn = spawnPoints[math.random(1, #spawnPoints)]
            if randomSpawn:IsA("BasePart") then
                character:MoveTo(randomSpawn.Position)
            end
        end
    end
})

ExtrTab:CreateButton({
    Name = "Teleport To VIP Club (MUST UNLOCK FIRST)",
    Callback = function()
        local player = game.Players.LocalPlayer
        local character = player.Character
        local survivorSpawns = game.Workspace:FindFirstChild("Lobby"):FindFirstChild("LargeSofa")

        if survivorSpawns and character then
            local spawnPoints = survivorSpawns:GetChildren()
            local randomSpawn = spawnPoints[math.random(1, #spawnPoints)]
            if randomSpawn:IsA("BasePart") then
                character:MoveTo(randomSpawn.Position)
            end
        end
    end
})

ExtrTab:CreateParagraph({Title = "", Content = "Teleport To VIP Club: You Must Go To The Game Tab And Unlock The Vip Club For This To Work."})

ExtrTab:CreateButton({
    Name = "Teleport To Slasher",
    Callback = function()
        -- Find the player with 20 walk speed
        local targetPlayer = nil
        for _, player in ipairs(game.Players:GetPlayers()) do
            if player.Character and player.Character:FindFirstChild("Humanoid") then
                local humanoid = player.Character.Humanoid
                if humanoid.WalkSpeed == 20 then
                    targetPlayer = player
                    break
                end
            end
        end

        -- Teleport the player to the target player
        if targetPlayer then
            local localPlayer = game.Players.LocalPlayer
            if localPlayer.Character and localPlayer.Character:FindFirstChild("HumanoidRootPart") then
                localPlayer.Character.HumanoidRootPart.CFrame = targetPlayer.Character.HumanoidRootPart.CFrame
            end
        else
            warn("Slasher has not been picked yet!.")
        end
    end
})

ExtrTab:CreateButton({
    Name = "Teleport To Random Player",
    Callback = function()
        local players = game:GetService("Players")
        local localPlayer = players.LocalPlayer
        local blacklist = {} -- List to store players to avoid teleporting to
        
        -- Function to check if player is blacklisted
        local function isBlacklisted(player)
            for _, blacklistedPlayer in ipairs(blacklist) do
                if blacklistedPlayer == player then
                    return true
                end
            end
            return false
        end

        -- Function to teleport to a player
        local function teleportToPlayer(player)
            if player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                localPlayer.Character:SetPrimaryPartCFrame(player.Character.HumanoidRootPart.CFrame)
            end
        end

        -- Populate blacklist based on WalkSpeed condition
        for _, player in pairs(players:GetPlayers()) do
            if player ~= localPlayer and player.Character and player.Character:FindFirstChild("Humanoid") then
                local walkSpeed = player.Character.Humanoid.WalkSpeed
                if walkSpeed == 20 or walkSpeed == 22.5 then
                    table.insert(blacklist, player)
                end
            end
        end

        -- Get a list of valid players for teleporting
        local validPlayers = {}
        for _, player in pairs(players:GetPlayers()) do
            if player ~= localPlayer and player.Character and not player.Character:FindFirstChild("DisplayGun") and not isBlacklisted(player) then
                table.insert(validPlayers, player)
            end
        end

        -- Teleport to a random valid player
        if #validPlayers > 0 then
            local randomPlayer = validPlayers[math.random(1, #validPlayers)]
            teleportToPlayer(randomPlayer)
        else
            print("No valid players found.")
        end
    end
})

ExtrTab:CreateButton({
    Name = "Teleport To Random Player (Lobby)",
    Callback = function()
        local players = game:GetService("Players")
        local localPlayer = players.LocalPlayer

        -- Function to teleport to a player
        local function teleportToPlayer(player)
            if player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                localPlayer.Character:SetPrimaryPartCFrame(player.Character.HumanoidRootPart.CFrame)
            end
        end

        -- Get a list of players that have DisplayGun
        local validPlayers = {}
        for _, player in pairs(players:GetPlayers()) do
            if player ~= localPlayer and player.Character and player.Character:FindFirstChild("DisplayGun") then
                table.insert(validPlayers, player)
            end
        end

        -- Teleport to a random valid player
        if #validPlayers > 0 then
            local randomPlayer = validPlayers[math.random(1, #validPlayers)]
            teleportToPlayer(randomPlayer)
        else
            print("No.")
        end
    end
})

ExtrTab:CreateParagraph({Title = "", Content = "Teleport To Random Player (Lobby): Teleports You To Players That Are JUST in the lobby."})

ExtrTab:CreateButton({
    Name = "Teleport To Slasher Spawn",
    Callback = function()
        local player = game.Players.LocalPlayer
        local character = player.Character
        local survivorSpawns = game.Workspace:FindFirstChild("CurrentMap"):FindFirstChild("KillerSpawns")

        if survivorSpawns and character then
            local spawnPoints = survivorSpawns:GetChildren()
            local randomSpawn = spawnPoints[math.random(1, #spawnPoints)]
            if randomSpawn:IsA("BasePart") then
                character:MoveTo(randomSpawn.Position)
            end
        end
    end
})

local clickDetector = Instance.new("ClickDetector") -- Create a click detector
local indicatorActive = false
local teleportConnection

-- Toggle for activating the click detector and teleportation
ExtrTab:CreateToggle({
    Name = "Click To Teleport",
    Callback = function(state)
        if state then
            -- Activate click detector
            clickDetector.Parent = workspace

            local player = game.Players.LocalPlayer
            local mouse = player:GetMouse()

            -- Disconnect any existing teleport connection
            if teleportConnection then
                teleportConnection:Disconnect()
            end

            -- Teleport to clicked position
            teleportConnection = mouse.Button1Down:Connect(function()
                player.Character:SetPrimaryPartCFrame(CFrame.new(mouse.Hit.p))
            end)

            indicatorActive = true
        else
            -- Deactivate click detector and disconnect teleport connection
            if clickDetector then
                clickDetector.Parent = nil
            end
            if teleportConnection then
                teleportConnection:Disconnect()
            end
            indicatorActive = false
        end
    end
})

GameTab:CreateSection("Game")

GameTab:CreateButton({
    Name = "Remove Barriers (Lobby)",
    Callback = function()
        local workspace = game:GetService("Workspace")
        local lobbyFolder = workspace:FindFirstChild("Lobby")

        if lobbyFolder then
            -- Remove the "Invis" model if it exists
            local invisModel = lobbyFolder:FindFirstChild("Invis")
            if invisModel and invisModel:IsA("Model") then
                invisModel:Destroy()
            end

            -- Check for and delete 5 parts named "Part"
            local partsRemoved = 0
            for _, item in ipairs(workspace:GetChildren()) do
                if item:IsA("BasePart") and item.Name == "Part" then
                    item:Destroy()
                    partsRemoved = partsRemoved + 1
                    if partsRemoved >= 5 then
                        break
                    end
                end
            end
        end
    end
})

GameTab:CreateParagraph({Title = "", Content = "Remove Barriers (Lobby): Removes All Barriers In The LOBBY Only."})

GameTab:CreateButton({
    Name = "Remove Barriers (Current Map)",
    Callback = function()
        local workspace = game:GetService("Workspace")
        local currentMap = workspace:FindFirstChild("CurrentMap")
        local invisWalls = nil

        if currentMap then
            -- First search in the 'Other' folder
            local otherFolder = currentMap:FindFirstChild("Other")
            if otherFolder then
                invisWalls = otherFolder:FindFirstChild("InvisWalls")
            end

            -- If not found, search in the 'Extra' model
            if not invisWalls then
                local extraModel = currentMap:FindFirstChild("Extra")
                if extraModel and extraModel:IsA("Model") then
                    invisWalls = extraModel:FindFirstChild("InvisWalls")
                end
            end

            -- If still not found, search directly in 'CurrentMap'
            if not invisWalls then
                invisWalls = currentMap:FindFirstChild("InvisWalls")
            end

            -- If found, destroy the model
            if invisWalls and invisWalls:IsA("Model") then
                invisWalls:Destroy()
            end
        end
    end
})

GameTab:CreateParagraph({Title = "", Content = "Remove Barriers (Current Map): Removes All Barriers In The CURRENT Map Only."})

local collectCoinsToggle = false
local runService = game:GetService("RunService")
local connection

GameTab:CreateToggle({
    Name = "Collect All Coins",
    Callback = function(state)
        collectCoinsToggle = state

        local player = game.Players.LocalPlayer

        -- Function to simulate touch on the coin
        local function simulateTouch(coin)
            firetouchinterest(player.Character.HumanoidRootPart, coin, 0) -- Simulate touch start
            firetouchinterest(player.Character.HumanoidRootPart, coin, 1) -- Simulate touch end
        end

        -- Function to collect coins
        local function collectCoins()
            local character = player.Character or player.CharacterAdded:Wait()
            local hrp = character:WaitForChild("HumanoidRootPart")
            local coinsModel = workspace:FindFirstChild("CurrentMap"):FindFirstChild("Coins")

            if coinsModel then
                local coins = coinsModel:GetChildren()
                for _, coin in pairs(coins) do
                    if coin:IsA("BasePart") and coin.Name == "Coin" then
                        -- Bring the coin to the player's character (HumanoidRootPart)
                        coin.CFrame = hrp.CFrame * CFrame.new(0, 0, -5) -- Move the coin near the player
                        simulateTouch(coin) -- Simulate touch while the coin is near the player
                    end
                end
            end
        end

        -- Toggle collection logic
        if collectCoinsToggle and not connection then
            connection = runService.Heartbeat:Connect(function()
                collectCoins()
                wait(0.05) -- Short delay to prevent crashes and optimize performance
            end)
        elseif not collectCoinsToggle and connection then
            connection:Disconnect()
            connection = nil
        end
    end
})

-- Define a function for better organization
local function unlockVIPClub()
    local workspace = game:GetService("Workspace")
    local ClubBlock = workspace:FindFirstChild("ClubBlock")
    
    if ClubBlock then
        ClubBlock:Destroy()
    else
        warn("ClubBlock not found!")
    end
end

-- Create the button
GameTab:CreateButton({
    Name = "Unlock VIP Club",
    Callback = unlockVIPClub,
})

elseif currentGameId ~= correctGameId then
    local TeleportService = game:GetService("TeleportService")
    TeleportService:Teleport(1022605215, game.Players.LocalPlayer)
end
