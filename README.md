local ScreenGui = Instance.new("ScreenGui")
local main = Instance.new("Frame")
local WalkOnWalls = Instance.new("TextButton")
local Fly = Instance.new("TextButton")
local Sh1t = Instance.new("TextBox")
local Commingsoon = Instance.new("TextBox")



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
	loadstring(game:HttpGet('https://raw.githubusercontent.com/finnpp/adopt.me.hub/main/README.md'))()
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
	loadstring(game:HttpGet('https://raw.githubusercontent.com/finnpp/gdfg/main/README.md'))()
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
