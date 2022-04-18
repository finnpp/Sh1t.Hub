local ScreenGui = Instance.new("ScreenGui")
local main = Instance.new("Frame")
local WalkOnWalls = Instance.new("TextButton")
local Fly = Instance.new("TextButton")
local Sh1t = Instance.new("TextBox")
local Commingsoon = Instance.new("TextBox")
local Respawn = Instance.new("TextButton")



ScreenGui.Parent = game.CoreGui

main.Name = "main"
main.Parent = ScreenGui
main.BackgroundColor3 = Color3.fromRGB(177, 137, 255)
main.BackgroundTransparency = 0.600
main.BorderSizePixel = 0
main.Position = UDim2.new(0.302533537, 0, 0.329920352, 0)
main.Size = UDim2.new(0, 530, 0, 299)
main.Active = true
main.Draggable = true


WalkOnWalls.Name = "WalkOnWalls"
WalkOnWalls.Parent = main
WalkOnWalls.BackgroundColor3 = Color3.fromRGB(122, 96, 255)
WalkOnWalls.Position = UDim2.new(0.0952872336, 0, 0.250885606, 0)
WalkOnWalls.Size = UDim2.new(0, 133, 0, 34)
WalkOnWalls.Font = Enum.Font.FredokaOne
WalkOnWalls.Text = "WalkOnWalls"
WalkOnWalls.TextColor3 = Color3.fromRGB(0, 0, 0)
WalkOnWalls.TextSize = 21.000
WalkOnWalls.MouseButton1Down:connect(function()
	local LocalPlayer = Players.LocalPlayer
	if not LocalPlayer then
		Players:GetPropertyChangedSignal("LocalPlayer"):Wait()
		LocalPlayer = Players.LocalPlayer
	end

	local function waitForChildOfClass(parent, class)
		local child = parent:FindFirstChildOfClass(class)
		while not child or child.ClassName ~= class do
			child = parent.ChildAdded:Wait()
		end
		return child
	end

	local PlayerGui = waitForChildOfClass(LocalPlayer, "PlayerGui")

	local TOAST_OPEN_SIZE = UDim2.new(0, 326, 0, 58)
	local TOAST_CLOSED_SIZE = UDim2.new(0, 80, 0, 58)
	local TOAST_BACKGROUND_COLOR = Color3.fromRGB(32, 32, 32)
	local TOAST_BACKGROUND_TRANS = 0.4
	local TOAST_FOREGROUND_COLOR = Color3.fromRGB(200, 200, 200)
	local TOAST_FOREGROUND_TRANS = 0

	-- Convenient syntax for creating a tree of instanes
	local function create(className)
		return function(props)
			local inst = Instance.new(className)
			local parent = props.Parent
			props.Parent = nil
			for name, val in pairs(props) do
				if type(name) == "string" then
					inst[name] = val
				else
					val.Parent = inst
				end
			end
			-- Only set parent after all other properties are initialized
			inst.Parent = parent
			return inst
		end
	end

	local initialized = false

	local uiRoot
	local toast
	local toastIcon
	local toastUpperText
	local toastLowerText

	local function initializeUI()
		assert(not initialized)

		uiRoot = create("ScreenGui"){
			Name = "RbxCameraUI",
			AutoLocalize = false,
			Enabled = true,
			DisplayOrder = -1, -- Appears behind default developer UI
			IgnoreGuiInset = false,
			ResetOnSpawn = false,
			ZIndexBehavior = Enum.ZIndexBehavior.Sibling,

			create("ImageLabel"){
				Name = "Toast",
				Visible = false,
				AnchorPoint = Vector2.new(0.5, 0),
				BackgroundTransparency = 1,
				BorderSizePixel = 0,
				Position = UDim2.new(0.5, 0, 0, 8),
				Size = TOAST_CLOSED_SIZE,
				Image = "rbxasset://textures/ui/Camera/CameraToast9Slice.png",
				ImageColor3 = TOAST_BACKGROUND_COLOR,
				ImageRectSize = Vector2.new(6, 6),
				ImageTransparency = 1,
				ScaleType = Enum.ScaleType.Slice,
				SliceCenter = Rect.new(3, 3, 3, 3),
				ClipsDescendants = true,

				create("Frame"){
					Name = "IconBuffer",
					BackgroundTransparency = 1,
					BorderSizePixel = 0,
					Position = UDim2.new(0, 0, 0, 0),
					Size = UDim2.new(0, 80, 1, 0),

					create("ImageLabel"){
						Name = "Icon",
						AnchorPoint = Vector2.new(0.5, 0.5),
						BackgroundTransparency = 1,
						Position = UDim2.new(0.5, 0, 0.5, 0),
						Size = UDim2.new(0, 48, 0, 48),
						ZIndex = 2,
						Image = "rbxasset://textures/ui/Camera/CameraToastIcon.png",
						ImageColor3 = TOAST_FOREGROUND_COLOR,
						ImageTransparency = 1,
					}
				},

				create("Frame"){
					Name = "TextBuffer",
					BackgroundTransparency = 1,
					BorderSizePixel = 0,
					Position = UDim2.new(0, 80, 0, 0),
					Size = UDim2.new(1, -80, 1, 0),
					ClipsDescendants = true,

					create("TextLabel"){
						Name = "Upper",
						AnchorPoint = Vector2.new(0, 1),
						BackgroundTransparency = 1,
						Position = UDim2.new(0, 0, 0.5, 0),
						Size = UDim2.new(1, 0, 0, 19),
						Font = Enum.Font.GothamSemibold,
						Text = "Camera control enabled",
						TextColor3 = TOAST_FOREGROUND_COLOR,
						TextTransparency = 1,
						TextSize = 19,
						TextXAlignment = Enum.TextXAlignment.Left,
						TextYAlignment = Enum.TextYAlignment.Center,
					},

					create("TextLabel"){
						Name = "Lower",
						AnchorPoint = Vector2.new(0, 0),
						BackgroundTransparency = 1,
						Position = UDim2.new(0, 0, 0.5, 3),
						Size = UDim2.new(1, 0, 0, 15),
						Font = Enum.Font.Gotham,
						Text = "Right mouse button to toggle",
						TextColor3 = TOAST_FOREGROUND_COLOR,
						TextTransparency = 1,
						TextSize = 15,
						TextXAlignment = Enum.TextXAlignment.Left,
						TextYAlignment = Enum.TextYAlignment.Center,
					},
				},
			},

			Parent = PlayerGui,
		}

		toast = uiRoot.Toast
		toastIcon = toast.IconBuffer.Icon
		toastUpperText = toast.TextBuffer.Upper
		toastLowerText = toast.TextBuffer.Lower

		initialized = true
	end

	local CameraUI = {}

	do
		-- Instantaneously disable the toast or enable for opening later on. Used when switching camera modes.
		function CameraUI.setCameraModeToastEnabled(enabled)
			if not enabled and not initialized then
				return
			end

			if not initialized then
				initializeUI()
			end

			toast.Visible = enabled
			if not enabled then
				CameraUI.setCameraModeToastOpen(false)
			end
		end

		local tweenInfo = TweenInfo.new(0.25, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)

		-- Tween the toast in or out. Toast must be enabled with setCameraModeToastEnabled.
		function CameraUI.setCameraModeToastOpen(open)
			assert(initialized)

			TweenService:Create(toast, tweenInfo, {
				Size = open and TOAST_OPEN_SIZE or TOAST_CLOSED_SIZE,
				ImageTransparency = open and TOAST_BACKGROUND_TRANS or 1,
			}):Play()

			TweenService:Create(toastIcon, tweenInfo, {
				ImageTransparency = open and TOAST_FOREGROUND_TRANS or 1,
			}):Play()

			TweenService:Create(toastUpperText, tweenInfo, {
				TextTransparency = open and TOAST_FOREGROUND_TRANS or 1,
			}):Play()

			TweenService:Create(toastLowerText, tweenInfo, {
				TextTransparency = open and TOAST_FOREGROUND_TRANS or 1,
			}):Play()
		end
	end

	return CameraUI
end)


Fly.Name = "Fly"
Fly.Parent = main
Fly.BackgroundColor3 = Color3.fromRGB(122, 96, 255)
Fly.Position = UDim2.new(0.0952872336, 0, 0.411420703, 0)
Fly.Size = UDim2.new(0, 133, 0, 34)
Fly.Font = Enum.Font.FredokaOne
Fly.Text = "Fly"
Fly.TextColor3 = Color3.fromRGB(0, 0, 0)
Fly.TextSize = 29.000
Fly.MouseButton1Down:connect(function()
	repeat wait() 
	until game.Players.LocalPlayer and game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:findFirstChild("Torso") and game.Players.LocalPlayer.Character:findFirstChild("Humanoid") 
	local mouse = game.Players.LocalPlayer:GetMouse() 
	repeat wait() until mouse
	local plr = game.Players.LocalPlayer 
	local torso = plr.Character.Torso 
	local flying = false
	local deb = true 
	local ctrl = {f = 0, b = 0, l = 0, r = 0} 
	local lastctrl = {f = 0, b = 0, l = 0, r = 0} 
	local maxspeed = 50 
	local speed = 0 

	function Fly() 
		local bg = Instance.new("BodyGyro", torso) 
		bg.P = 9e4 
		bg.maxTorque = Vector3.new(9e9, 9e9, 9e9) 
		bg.cframe = torso.CFrame 
		local bv = Instance.new("BodyVelocity", torso) 
		bv.velocity = Vector3.new(0,0.1,0) 
		bv.maxForce = Vector3.new(9e9, 9e9, 9e9) 
		repeat wait() 
			plr.Character.Humanoid.PlatformStand = true 
			if ctrl.l + ctrl.r ~= 0 or ctrl.f + ctrl.b ~= 0 then 
				speed = speed+.5+(speed/maxspeed) 
				if speed > maxspeed then 
					speed = maxspeed 
				end 
			elseif not (ctrl.l + ctrl.r ~= 0 or ctrl.f + ctrl.b ~= 0) and speed ~= 0 then 
				speed = speed-1 
				if speed < 0 then 
					speed = 0 
				end 
			end 
			if (ctrl.l + ctrl.r) ~= 0 or (ctrl.f + ctrl.b) ~= 0 then 
				bv.velocity = ((game.Workspace.CurrentCamera.CoordinateFrame.lookVector * (ctrl.f+ctrl.b)) + ((game.Workspace.CurrentCamera.CoordinateFrame * CFrame.new(ctrl.l+ctrl.r,(ctrl.f+ctrl.b)*.2,0).p) - game.Workspace.CurrentCamera.CoordinateFrame.p))*speed 
				lastctrl = {f = ctrl.f, b = ctrl.b, l = ctrl.l, r = ctrl.r} 
			elseif (ctrl.l + ctrl.r) == 0 and (ctrl.f + ctrl.b) == 0 and speed ~= 0 then 
				bv.velocity = ((game.Workspace.CurrentCamera.CoordinateFrame.lookVector * (lastctrl.f+lastctrl.b)) + ((game.Workspace.CurrentCamera.CoordinateFrame * CFrame.new(lastctrl.l+lastctrl.r,(lastctrl.f+lastctrl.b)*.2,0).p) - game.Workspace.CurrentCamera.CoordinateFrame.p))*speed 
			else 
				bv.velocity = Vector3.new(0,0.1,0) 
			end 
			bg.cframe = game.Workspace.CurrentCamera.CoordinateFrame * CFrame.Angles(-math.rad((ctrl.f+ctrl.b)*50*speed/maxspeed),0,0) 
		until not flying 
		ctrl = {f = 0, b = 0, l = 0, r = 0} 
		lastctrl = {f = 0, b = 0, l = 0, r = 0} 
		speed = 0 
		bg:Destroy() 
		bv:Destroy() 
		plr.Character.Humanoid.PlatformStand = false 
	end 
	mouse.KeyDown:connect(function(key) 
		if key:lower() == "e" then 
			if flying then flying = false 
			else 
				flying = true 
				Fly() 
			end 
		elseif key:lower() == "w" then 
			ctrl.f = 1 
		elseif key:lower() == "s" then 
			ctrl.b = -1 
		elseif key:lower() == "a" then 
			ctrl.l = -1 
		elseif key:lower() == "d" then 
			ctrl.r = 1 
		end 
	end) 
	mouse.KeyUp:connect(function(key) 
		if key:lower() == "w" then 
			ctrl.f = 0 
		elseif key:lower() == "s" then 
			ctrl.b = 0 
		elseif key:lower() == "a" then 
			ctrl.l = 0 
		elseif key:lower() == "d" then 
			ctrl.r = 0 
		end 
	end)
	Fly()
end)


Sh1t.Name = "Sh1t"
Sh1t.Parent = main
Sh1t.BackgroundColor3 = Color3.fromRGB(164, 99, 255)
Sh1t.Position = UDim2.new(0.0190225858, 0, 0.0334448144, 0)
Sh1t.Size = UDim2.new(0, 101, 0, 25)
Sh1t.Font = Enum.Font.FredokaOne
Sh1t.Text = "Sh1t.W0rld"
Sh1t.TextColor3 = Color3.fromRGB(0, 0, 0)
Sh1t.TextSize = 19.000



Commingsoon.Name = "Commingsoon"
Commingsoon.Parent = main
Commingsoon.BackgroundColor3 = Color3.fromRGB(51, 95, 255)
Commingsoon.Position = UDim2.new(0.726415098, 0, 0.862876236, 0)
Commingsoon.Size = UDim2.new(0, 131, 0, 33)
Commingsoon.Font = Enum.Font.SourceSans
Commingsoon.Text = "More Comming Soon"
Commingsoon.TextColor3 = Color3.fromRGB(0, 0, 0)
Commingsoon.TextSize = 14.000



Respawn.Name = "Respawn"
Respawn.Parent = main
Respawn.BackgroundColor3 = Color3.fromRGB(125, 116, 255)
Respawn.BorderColor3 = Color3.fromRGB(27, 42, 53)
Respawn.Position = UDim2.new(0.094339624, 0, 0.571906328, 0)
Respawn.Size = UDim2.new(0, 133, 0, 36)
Respawn.Font = Enum.Font.FredokaOne
Respawn.Text = "Respawn"
Respawn.TextColor3 = Color3.fromRGB(0, 0, 0)
Respawn.TextSize = 29.000
Respawn.MouseButton1Down:connect(function()
	local a1 = Instance.new("Model", workspace)
	local a2 = Instance.new("Part", a1)
	a2.CanCollide = true
	a2.Anchored = true
	a2.CFrame = CFrame.new(10000, 10000, 10000)
	a2.Name = "Torso"
	local a3 = Instance.new("Humanoid", a1)
	a3.MaxHealth=100;a3.Health=100
	game.Players.LocalPlayer.Character = a1
	a3.Health=0
end)
