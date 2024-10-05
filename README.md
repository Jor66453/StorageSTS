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
    Name = "SURVIVE THE SLASHER HUB V4",
    LoadingTitle = "Survive The Slasher V4",
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
                        local head = player.Character:FindFirstChild("Head")
                        local healthGui = head and head:FindFirstChild("HealthGui")

                        -- If ESP does not exist, create it
                        if not esp then
                            createOutline(player)
                            esp = player.Character:FindFirstChild("ESP")
                        end

                        -- Check if 'esp' was successfully created before modifying it
                        if esp and esp:IsA("Highlight") then
                            local color
                            if healthGui then
                                color = Color3.fromRGB(204, 204, 0) -- Yellow if HealthGui exists
                            else
                                -- Change color based on WalkSpeed if HealthGui is removed
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
                                elseif walkSpeed == 32 then
                                    color = Color3.fromRGB(252, 76, 2)
                                else
                                    color = Color3.fromRGB(1, 50, 32)
                                end
                            end
                            esp.FillColor = color -- Adjust fill color based on healthGui or walk speed
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
                        local head = player.Character:FindFirstChild("Head")
                        local healthGui = head and head:FindFirstChild("HealthGui")

                        if walkSpeed ~= 20 and walkSpeed ~= 22.5 then
                            if not esp then
                                createOutline(player)
                            end

                            local color
                            if healthGui then
                                color = Color3.fromRGB(204, 204, 0) -- Yellow if HealthGui exists
                            else
                                -- Change color based on WalkSpeed if HealthGui is removed
                                if walkSpeed == 16 then
                                    color = Color3.fromRGB(0,255,127) -- Green for 16
                                elseif walkSpeed == 5 then
                                    color = Color3.fromRGB(204, 204, 0) -- Yellow for 5
                                elseif walkSpeed == 24 then
                                    color = Color3.fromRGB(0,255,127) -- Green for 24
                                elseif walkSpeed == 25 then
                                    color = Color3.fromRGB(172, 79, 198) -- Purple for 25
                                elseif walkSpeed == 32 then
                                    color = Color3.fromRGB(252, 76, 2)
                                else 
                                    color = Color3.fromRGB(1, 50, 32)
                                end
                            end

                            if color and esp and esp:IsA("Highlight") then
                                esp.FillColor = color
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

SurviTab:CreateButton({
    Name = "Heal Me",
    Callback = function()
        local player = game.Players.LocalPlayer
        local character = player.Character
        local humanoid = character:FindFirstChildOfClass("Humanoid")
        local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
        local originalPosition = humanoidRootPart.CFrame.Position
        local teleporting = true

        -- Check for DisplayGun and other conditions
        local function checkConditions()
            if character:FindFirstChild("DisplayGun") then
                Rayfield:Notify({
                    Title = "Warning",
                    Content = "You can't use this in the lobby.",
                    Duration = 6.5,
                    Actions = {
                        Ignore = { Name = "Close", Callback = function() end }
                    }
                })
                return false
            elseif humanoid.WalkSpeed == 20 or humanoid.WalkSpeed == 22.5 then
                Rayfield:Notify({
                    Title = "Warning",
                    Content = "You can't use this while you are the slasher.",
                    Duration = 6.5,
                    Actions = {
                        Ignore = { Name = "Close", Callback = function() end }
                    }
                })
                return false
            elseif humanoid.WalkSpeed == 16 then
                Rayfield:Notify({
                    Title = "Warning",
                    Content = "You must be injured by the slasher to use this.",
                    Duration = 6.5,
                    Actions = {
                        Ignore = { Name = "Close", Callback = function() end }
                    }
                })
                return false
            end
            return true
        end

        -- Function to find a valid target player (excluding WalkSpeed 20 and 22.5)
        local function findValidTargetPlayer()
            for _, otherPlayer in pairs(game.Players:GetPlayers()) do
                if otherPlayer ~= player and otherPlayer.Character then
                    local otherCharacter = otherPlayer.Character
                    local otherHumanoid = otherCharacter:FindFirstChildOfClass("Humanoid")
                    local otherHead = otherCharacter:FindFirstChild("Head")
                    local healthGui = otherHead and otherHead:FindFirstChild("HealthGui")
                    local displayGun = otherCharacter:FindFirstChild("DisplayGun")

                    -- Only target if they don't have HealthGui, DisplayGun, and WalkSpeed is not 20 or 22.5
                    if otherHumanoid and not healthGui and not displayGun and (otherHumanoid.WalkSpeed ~= 20 and otherHumanoid.WalkSpeed ~= 22.5) then
                        return otherPlayer
                    end
                end
            end
            return nil
        end

        -- Function to teleport to a valid player
        local function teleportToTarget(targetPlayer)
            if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
                humanoidRootPart.CFrame = targetPlayer.Character.HumanoidRootPart.CFrame
            end
        end

        -- Function to spam teleport to valid targets
        local function spamTeleport()
            local targetPlayer = findValidTargetPlayer()

            -- Spam teleport to valid player
            while teleporting do
                if targetPlayer then
                    teleportToTarget(targetPlayer)
                else
                    targetPlayer = findValidTargetPlayer() -- Find new target if conditions change
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
    Name = "Heal A Player",
    Callback = function()
        local Players = game:GetService("Players")
        local LocalPlayer = Players.LocalPlayer
        local RunService = game:GetService("RunService")

        local character = LocalPlayer.Character
        local originalPosition

        -- Save original position if the character and HumanoidRootPart exist
        if character and character:FindFirstChild("HumanoidRootPart") then
            originalPosition = character.HumanoidRootPart.CFrame
        end

        local function TeleportToPlayer(targetPlayer)
            local character = LocalPlayer.Character
            if character and character:FindFirstChild("HumanoidRootPart") then
                local targetCharacter = targetPlayer.Character
                if targetCharacter and targetCharacter:FindFirstChild("HumanoidRootPart") then
                    -- Teleport the local player to the target player's position
                    character.HumanoidRootPart.CFrame = targetCharacter.HumanoidRootPart.CFrame
                end
            end
        end

        local function HealSinglePlayer()
            for _, player in ipairs(Players:GetPlayers()) do
                if player ~= LocalPlayer then
                    local targetCharacter = player.Character
                    if targetCharacter and targetCharacter:FindFirstChild("Head") then
                        local healthGui = targetCharacter.Head:FindFirstChild("HealthGui")
                        if healthGui then
                            -- Teleport loop until HealthGui is gone
                            while healthGui and healthGui.Parent do
                                TeleportToPlayer(player)
                                RunService.Stepped:Wait() -- Wait a frame to prevent script lag
                                healthGui = targetCharacter.Head:FindFirstChild("HealthGui") -- Update to see if HealthGui is removed
                            end
                            -- Once HealthGui is gone, teleport back to original position
                            if originalPosition and character and character:FindFirstChild("HumanoidRootPart") then
                                character.HumanoidRootPart.CFrame = originalPosition
                            end
                            break -- Exit after teleporting to the first player with HealthGui
                        end
                    end
                end
            end
        end

        HealSinglePlayer()
    end
})

local seslreek = SurviTab:CreateButton({
    Name = "Spectate Slasher",
    Callback = function()
        local Players = game:GetService("Players")
        local player = Players.LocalPlayer
        local camera = workspace.CurrentCamera
        local spectateActive = true  -- Track if spectate is active

        -- Function to reset camera to its original state
        local function resetCamera()
            camera.CameraType = Enum.CameraType.Custom
            camera.CameraSubject = player.Character:FindFirstChild("Humanoid")
            camera.FieldOfView = 70
            player.CameraMaxZoomDistance = 128
            player.CameraMinZoomDistance = 0
            player.CameraMode = Enum.CameraMode.Classic
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
                        player.CameraMode = Enum.CameraMode.Classic  -- Ensure camera mode is Classic
                        return
                    end
                end
            end
        end

        -- Function to create and handle the exit button
        local function createExitButton()
            local screenGui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
            local button = Instance.new("TextButton", screenGui)
            local uiCorner = Instance.new("UICorner", button)
            
            button.Size = UDim2.new(0, 250, 0, 75)
            button.Position = UDim2.new(0.5, -125, 0.8, 0)
            button.Text = "EXIT"
            button.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
            button.TextColor3 = Color3.fromRGB(255, 255, 255)
            button.Font = Enum.Font.GothamBlack
            button.TextScaled = true
            button.TextStrokeTransparency = 0
            button.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
            
            uiCorner.CornerRadius = UDim.new(0, 15)

            button.MouseButton1Click:Connect(function()
                resetCamera()
                screenGui:Destroy()  -- Destroy the exit button
                spectateActive = false  -- End spectating
            end)
        end

        -- Function to monitor player's death and reset camera
        local function monitorPlayerDeath()
            local humanoid = player.Character and player.Character:FindFirstChild("Humanoid")
            if humanoid then
                humanoid.Died:Connect(function()
                    resetCamera()
                    -- Destroy the button on death
                    local playerGui = player:FindFirstChild("PlayerGui")
                    if playerGui then
                        local screenGui = playerGui:FindFirstChild("ScreenGui")
                        if screenGui then
                            screenGui:Destroy()
                        end
                    end
                    spectateActive = false  -- End spectating
                end)
            end
        end

        -- Function to monitor players' walk speed and reset the camera
        local function monitorWalkSpeed()
            while spectateActive do
                if not hasPlayersWithWalkSpeed({20, 22.5}) then
                    resetCamera()
                    -- Destroy the exit button if it's still present
                    local playerGui = player:FindFirstChild("PlayerGui")
                    if playerGui then
                        local screenGui = playerGui:FindFirstChild("ScreenGui")
                        if screenGui then
                            screenGui:Destroy()
                        end
                    end
                    break
                end
                task.wait(5)  -- Check every 5 seconds
            end
        end

        -- Check if there are players with the specified walk speeds
        if hasPlayersWithWalkSpeed({20, 22.5}) then
            moveToPlayerWithWalkSpeed({20, 22.5})  -- Move camera to the player
            createExitButton()  -- Create the exit button
            task.spawn(monitorWalkSpeed)  -- Start monitoring the players' walk speeds
            monitorPlayerDeath()  -- Start monitoring player's death
        else
            -- Show notification if no player is found with the specified walk speeds
            Rayfield:Notify({
                Title = "Notice!",
                Content = "Please Wait Until The Slasher Spawns Or Is Selected To Spectate.",
                Duration = 6.5,
                Image = nil,
                Actions = {
                    Ignore = {
                        Name = "Ok",
                        Callback = function() end
                    }
                }
            })
        end
    end,
})

SurviTab:CreateSection("Fake GamePass Perks")

SurviTab:CreateButton({
    Name = "Fake SpeedJuice Simulation",
    Callback = function()
        local player = game.Players.LocalPlayer
        local humanoid = player.Character.Humanoid
        local originalSpeed = humanoid.WalkSpeed
        
        humanoid.WalkSpeed = 25
        wait(6.5)
        humanoid.WalkSpeed = originalSpeed
    end
})

SurviTab:CreateButton({
    Name = "Fake SpeedShake Simulation",
    Callback = function()
        local player = game.Players.LocalPlayer
        local humanoid = player.Character.Humanoid
        local originalSpeed = humanoid.WalkSpeed
        
        humanoid.WalkSpeed = 32
        wait(4.5)
        humanoid.WalkSpeed = originalSpeed
    end
})

SurviTab:CreateButton({
    Name = "Fake DobuleJump Simulation",
    Callback = function()
        local UIS = game:GetService("UserInputService")
local player = game.Players.LocalPlayer
local character
local humanoid
 
local canDoubleJump = false
local hasDoubleJumped = false
local oldPower
local time_delay = 0.2
local jump_multiplier = 1 
function onJumpRequest()
	if not character or not humanoid or not character:IsDescendantOf(workspace) or humanoid:GetState() == Enum.HumanoidStateType.Dead then
		return
	end
 
	if canDoubleJump and not hasDoubleJumped then
		hasDoubleJumped = true
		humanoid.JumpPower = oldPower * jump_multiplier
		humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
	end
end
 
local function characterAdded(new)
	character = new
	humanoid = new:WaitForChild("Humanoid")
	hasDoubleJumped = false
	canDoubleJump = false
	oldPower = humanoid.JumpPower
 
	humanoid.StateChanged:connect(function(old, new)
		if new == Enum.HumanoidStateType.Landed then
			canDoubleJump = false
			hasDoubleJumped = false
			humanoid.JumpPower = oldPower
		elseif new == Enum.HumanoidStateType.Freefall then
			wait(time_delay)
			canDoubleJump = true
		end
	end)
end
 
if player.Character then
	characterAdded(player.Character)	
end
 
player.CharacterAdded:connect(characterAdded)
UIS.JumpRequest:connect(onJumpRequest)
    end
})

SlrTab:CreateSection("Slasher Perks")

-- Create the button
SlrTab:CreateButton({
    Name = "Slash All",
    Callback = function()

        local players = game:GetService("Players")
        local localPlayer = players.LocalPlayer

        -- Check the player's WalkSpeed
        local function checkValidWalkSpeed()
            local humanoid = localPlayer.Character and localPlayer.Character:FindFirstChild("Humanoid")
            if humanoid then
                local walkSpeed = humanoid.WalkSpeed
                return walkSpeed == 20 or walkSpeed == 22.5
            end
            return false
        end

        -- If WalkSpeed is not valid, send notification and stop execution
        if not checkValidWalkSpeed() then
            Rayfield:Notify({
                    Title = "Warning",
                    Content = "You must be the slasher to use this.",
                    Duration = 6.5,
                    Actions = {
                        Ignore = { Name = "Close", Callback = function() end }
                    }
                })
            return -- Stop the script from running
        end

        local originalPositions = {}

        -- Equip the only tool in the local player's inventory
        local function equipOnlyTool()
            local backpack = localPlayer:WaitForChild("Backpack")
            local tool = backpack:FindFirstChildOfClass("Tool")
            if tool then
                tool.Parent = localPlayer.Character
            end
        end

        -- Equip the tool before doing anything else
        equipOnlyTool()

        -- Check if the player has a tool
        local function hasTool()
            local backpack = localPlayer:WaitForChild("Backpack")
            return backpack:FindFirstChildOfClass("Tool") or (localPlayer.Character and localPlayer.Character:FindFirstChildOfClass("Tool"))
        end

        -- Store the original positions of all players
        for _, otherPlayer in ipairs(players:GetPlayers()) do
            if otherPlayer ~= localPlayer and otherPlayer.Character and otherPlayer.Character:FindFirstChild("HumanoidRootPart") then
                originalPositions[otherPlayer] = otherPlayer.Character.HumanoidRootPart.CFrame
            end
        end

        -- Bring all players close to the local player
        local bringConnection
        bringConnection = game:GetService("RunService").Heartbeat:Connect(function()
            for _, otherPlayer in ipairs(players:GetPlayers()) do
                if otherPlayer ~= localPlayer and otherPlayer.Character and otherPlayer.Character:FindFirstChild("HumanoidRootPart") then
                    otherPlayer.Character.HumanoidRootPart.CFrame = localPlayer.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, -2)
                end
            end
        end)

        -- Simulate an autoclicker that clicks 2 times per 0.1 seconds
        local function startAutoClicker()
            local clickConnection
            clickConnection = game:GetService("RunService").Heartbeat:Connect(function()
                for i = 1, 2 do
                    -- Simulate a click
                    mouse1click() -- Simulates a left mouse click
                end
                wait(0.05) -- Wait to match 2 clicks per 0.1 seconds
            end)

            -- Continuously check for tool in inventory and stop when no tool is left
            spawn(function()
                while hasTool() do
                    wait(0.1)
                end

                -- Disconnect autoclicker and stop bringing players once no tool is found
                if clickConnection then
                    clickConnection:Disconnect()
                end
                if bringConnection then
                    bringConnection:Disconnect()
                end
                Rayfield:Notify({
                    Title = "Slashed All!",
                    Content = "All Players Have Been Killed.",
                    Duration = 5,
                    Image = nil
                })
            end)
        end

        -- Start the autoclicker
        startAutoClicker()

    end
})

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- Slasher Vision Button
local slrbstm = SlrTab:CreateToggle({
    Name = "Slasher Vision",
    CurrentValue = false,
    Flag = "RedVisionToggle",
    Callback = function(Value)
        local player = game.Players.LocalPlayer
        local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")

        if not humanoid then
            return -- Exit if no humanoid is found
        end -- end if not humanoid

        if Value then
            -- Set screen red
            local redOverlay = Instance.new("ScreenGui", player.PlayerGui)
            redOverlay.Name = "RedOverlay"
            redOverlay.IgnoreGuiInset = true

            local redFrame = Instance.new("Frame", redOverlay)
            redFrame.Size = UDim2.new(1, 0, 1, 0)
            redFrame.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
            redFrame.BackgroundTransparency = 0.7
        else
            -- Remove red screen when toggled off
            if player.PlayerGui:FindFirstChild("RedOverlay") then
                player.PlayerGui.RedOverlay:Destroy()
            end -- end if RedOverlay exists
        end -- end if Value
    end -- end callback function
}) -- end CreateToggle for Slasher Vision

SlrTab:CreateSection("Fake GamePass Perks")

SlrTab:CreateButton({
    Name = "Fake Extra Slasher Speed",
    Callback = function()
        local player = game.Players.LocalPlayer
        local humanoid = player.Character.Humanoid
        
        humanoid.WalkSpeed = 22.5
    end
})

SlrTab:CreateButton({
    Name = "Fake DobuleJump Simulation",
    Callback = function()
        local UIS = game:GetService("UserInputService")
local player = game.Players.LocalPlayer
local character
local humanoid
 
local canDoubleJump = false
local hasDoubleJumped = false
local oldPower
local time_delay = 0.2
local jump_multiplier = 1 
function onJumpRequest()
	if not character or not humanoid or not character:IsDescendantOf(workspace) or humanoid:GetState() == Enum.HumanoidStateType.Dead then
		return
	end
 
	if canDoubleJump and not hasDoubleJumped then
		hasDoubleJumped = true
		humanoid.JumpPower = oldPower * jump_multiplier
		humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
	end
end
 
local function characterAdded(new)
	character = new
	humanoid = new:WaitForChild("Humanoid")
	hasDoubleJumped = false
	canDoubleJump = false
	oldPower = humanoid.JumpPower
 
	humanoid.StateChanged:connect(function(old, new)
		if new == Enum.HumanoidStateType.Landed then
			canDoubleJump = false
			hasDoubleJumped = false
			humanoid.JumpPower = oldPower
		elseif new == Enum.HumanoidStateType.Freefall then
			wait(time_delay)
			canDoubleJump = true
		end
	end)
end
 
if player.Character then
	characterAdded(player.Character)	
end
 
player.CharacterAdded:connect(characterAdded)
UIS.JumpRequest:connect(onJumpRequest)
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

local screenGui

GameTab:CreateToggle({
    Name = "Info",
    Callback = function(state)
        if state then
            -- Create the screen GUI only if it doesn't exist
            if not screenGui then
                screenGui = Instance.new("ScreenGui", game.Players.LocalPlayer.PlayerGui)

                -- Slasher Label
                local slasherTextLabel = Instance.new("TextLabel", screenGui)
                slasherTextLabel.Size = UDim2.new(0, 250, 0, 50)
                slasherTextLabel.Position = UDim2.new(0, 20, 1, -170)
                slasherTextLabel.BackgroundTransparency = 1
                slasherTextLabel.TextSize = 30
                slasherTextLabel.Font = Enum.Font.GothamBold
                slasherTextLabel.TextStrokeTransparency = 0.8
                slasherTextLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
                slasherTextLabel.Text = "Slasher: Waiting..."

                -- Role Label
                local roleTextLabel = Instance.new("TextLabel", screenGui)
                roleTextLabel.Size = UDim2.new(0, 250, 0, 50)
                roleTextLabel.Position = UDim2.new(0, 20, 1, -120)
                roleTextLabel.BackgroundTransparency = 1
                roleTextLabel.TextSize = 30
                roleTextLabel.Font = Enum.Font.GothamBold
                roleTextLabel.TextStrokeTransparency = 0.8
                roleTextLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
                roleTextLabel.Text = "Your Role: Waiting..."

                -- Distance Label (moved to the right)
                local distanceTextLabel = Instance.new("TextLabel", screenGui)
                distanceTextLabel.Size = UDim2.new(0, 250, 0, 50)
                distanceTextLabel.Position = UDim2.new(0, 189, 1, -70) -- Closer to the right
                distanceTextLabel.BackgroundTransparency = 1
                distanceTextLabel.TextSize = 30
                distanceTextLabel.Font = Enum.Font.GothamBold
                distanceTextLabel.TextStrokeTransparency = 0.8
                distanceTextLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
                distanceTextLabel.Text = "Your Distance From Slasher: Waiting..."

                -- Update Labels Continuously
                while state do
                    wait(1)

                    -- Get slasher player
                    local slasher = nil
                    for _, player in pairs(game.Players:GetPlayers()) do
                        local character = player.Character
                        if character and character:FindFirstChild("Humanoid") then
                            local walkspeed = character.Humanoid.WalkSpeed
                            if walkspeed == 20 or walkspeed == 22.5 then
                                slasher = player
                                break
                            end
                        end
                    end

                    -- Update Slasher Label
                    if slasher then
                        slasherTextLabel.Text = "Slasher: " .. slasher.Name
                        slasherTextLabel.TextColor3 = Color3.fromRGB(255, 0, 0) -- Red when slasher is found
                    else
                        slasherTextLabel.Text = "Slasher: Waiting..."
                        slasherTextLabel.TextColor3 = Color3.fromRGB(255, 255, 255) -- White when waiting
                    end

                    -- Update Role Label
                    local localPlayer = game.Players.LocalPlayer
                    local humanoid = localPlayer.Character and localPlayer.Character:FindFirstChild("Humanoid")
                    if humanoid then
                        local walkspeed = humanoid.WalkSpeed
                        local hasDisplayGun = localPlayer.Character:FindFirstChild("DisplayGun") ~= nil
                        if walkspeed == 20 or walkspeed == 22.5 then
                            roleTextLabel.Text = "Your Role: Slasher"
                            roleTextLabel.TextColor3 = Color3.fromRGB(255, 0, 0) -- Red for Slasher
                        elseif hasDisplayGun then
                            roleTextLabel.Text = "Your Role: Waiting..."
                        else
                            roleTextLabel.Text = "Your Role: Survivor"
                            roleTextLabel.TextColor3 = Color3.fromRGB(0, 255, 0) -- Green for Survivor
                        end
                    end

                    -- Update Distance Label
                    if slasher then
                        local slasherPos = slasher.Character.HumanoidRootPart.Position
                        local playerPos = localPlayer.Character.HumanoidRootPart.Position
                        local distance = (playerPos - slasherPos).Magnitude
                        if distance <= 50 then
                            distanceTextLabel.Text = "Your Distance From Slasher: Close (" .. math.floor(distance) .. " studs)"
                            distanceTextLabel.TextColor3 = Color3.fromRGB(255, 0, 0) -- Red for close
                        elseif distance <= 100 then
                            distanceTextLabel.Text = "Your Distance From Slasher: Away (" .. math.floor(distance) .. " studs)"
                            distanceTextLabel.TextColor3 = Color3.fromRGB(255, 165, 0) -- Orange for away
                        else
                            distanceTextLabel.Text = "Your Distance From Slasher: Far (" .. math.floor(distance) .. " studs)"
                            distanceTextLabel.TextColor3 = Color3.fromRGB(0, 255, 0) -- Green for far
                        end
                    else
                        distanceTextLabel.Text = "Your Distance From Slasher: Waiting..."
                        distanceTextLabel.TextColor3 = Color3.fromRGB(255, 255, 255) -- White when waiting
                    end
                end
            end
        else
            -- Destroy the screen GUI and its labels when toggle is off
            if screenGui then
                screenGui:Destroy()
                screenGui = nil  -- Make sure it can be recreated when toggled back on
            end
        end
    end
})

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

GameTab:CreateButton({
    Name = "Collect All Coins",
    Callback = function()
        local player = game.Players.LocalPlayer
        local character = player.Character or player.CharacterAdded:Wait()
        local hrp = character:WaitForChild("HumanoidRootPart")
        local mapFolder = workspace:FindFirstChild("CurrentMap")
        
        if mapFolder then
            local coinsModel = mapFolder:FindFirstChild("Coins")
            if coinsModel then
                local coins = coinsModel:GetChildren()
                
                local function collectCoin(coin)
                    if coin:IsA("BasePart") then
                        coin.CFrame = hrp.CFrame -- Move coin to player
                        firetouchinterest(coin, hrp, 0)
                        coin.CFrame = coin.CFrame * CFrame.new(0, 50, 0) -- Move coin back
                        firetouchinterest(coin, hrp, 1)
                    end
                end
                
                for _, coin in ipairs(coins) do
                    collectCoin(coin)
                end
            end
        end
    end
})

GameTab:CreateToggle({
    Name = "Auto Collect Coins",
    Default = false,
    Callback = function(state)
        toggle = state
        
        if toggle and not collecting then
            collecting = true

            spawn(function()
                game.Players.LocalPlayer.CharacterAdded:Connect(function()
                    if toggle then
                        collecting = false -- Reset collecting after respawn
                        wait(1) -- Ensure character is fully loaded
                        toggle = true -- Resume auto collection after respawn
                        collecting = true -- Re-enable collecting loop
                    end
                end)

                while toggle do
                    local player = game.Players.LocalPlayer
                    local character = player.Character or player.CharacterAdded:Wait()
                    local hrp = character:WaitForChild("HumanoidRootPart")
                    local mapFolder = workspace:FindFirstChild("CurrentMap")

                    if mapFolder then
                        local coinsModel = mapFolder:FindFirstChild("Coins")
                        if coinsModel then
                            for _, coin in pairs(coinsModel:GetChildren()) do
                                if not toggle then break end
                                if coin:IsA("BasePart") then
                                    -- Move coin to player
                                    coin.CFrame = hrp.CFrame
                                    firetouchinterest(coin, hrp, 0)
                                    -- Move coin back
                                    coin.CFrame = coin.CFrame * CFrame.new(0, 50, 0)
                                    firetouchinterest(coin, hrp, 1)
                                end
                            end
                        end
                    end
                    task.wait(0.01) -- Fast loop to minimize delays
                end
                collecting = false
            end)
        else
            collecting = false
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

-- Create the button
GameTab:CreateButton({
    Name = "Remove Traps (Khufu Map)",
    Callback = function()
        local currentMap = game.Workspace:FindFirstChild("CurrentMap")
        if currentMap then
            local extraModel = currentMap:FindFirstChild("Extra")
            if extraModel then
                for _, mesh in pairs(extraModel:GetChildren()) do
                    if mesh:IsA("Model") and mesh.Name == "Mesh" then
                        if mesh:FindFirstChild("Sword Stab") then
                            mesh:Destroy()
                        end
                    end
                end
            end
        end
    end
})

elseif currentGameId ~= correctGameId then
    local TeleportService = game:GetService("TeleportService")
    TeleportService:Teleport(1022605215, game.Players.LocalPlayer)
end
