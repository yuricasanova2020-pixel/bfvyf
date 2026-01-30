local p = game.Players.LocalPlayer
local UIS = game:GetService("UserInputService")

-- GUI
local g = Instance.new("ScreenGui")
g.Name = "HackMenu"
g.ResetOnSpawn = false
g.Parent = p:WaitForChild("PlayerGui")

--------------------------------------------------
-- BOLINHA FLUTUANTE
--------------------------------------------------
local ball = Instance.new("TextButton")
ball.Size = UDim2.new(0, 55, 0, 55)
ball.Position = UDim2.new(0, 15, 0.6, 0)
ball.Text = "⚡"
ball.BackgroundColor3 = Color3.fromRGB(140, 0, 200)
ball.TextColor3 = Color3.new(1,1,1)
ball.Font = Enum.Font.GothamBold
ball.TextSize = 24
ball.BorderSizePixel = 0
ball.ZIndex = 10
ball.Parent = g

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(1,0)
corner.Parent = ball

--------------------------------------------------
-- MENU
--------------------------------------------------
local x = Instance.new("Frame")
x.Size = UDim2.new(0, 300, 0, 280)
x.Position = UDim2.new(0.5, -150, 0.5, -140)
x.BackgroundColor3 = Color3.fromRGB(18,18,18)
x.BorderColor3 = Color3.fromRGB(160,0,255)
x.BorderSizePixel = 2
x.Visible = false
x.Parent = g

-- Título
local t = Instance.new("TextLabel")
t.Size = UDim2.new(1,0,0,35)
t.Text = "⚡ MENU ⚡"
t.BackgroundColor3 = Color3.fromRGB(25,25,25)
t.TextColor3 = Color3.fromRGB(180,0,255)
t.Font = Enum.Font.GothamBold
t.TextSize = 18
t.Parent = x

-- Minimizar
local min = Instance.new("TextButton")
min.Size = UDim2.new(0,30,0,30)
min.Position = UDim2.new(1,-35,0,2)
min.Text = "-"
min.BackgroundColor3 = Color3.fromRGB(40,40,40)
min.TextColor3 = Color3.new(1,1,1)
min.Font = Enum.Font.GothamBold
min.TextSize = 20
min.Parent = x

--------------------------------------------------
-- BOTÕES
--------------------------------------------------
local function makeBtn(text, y, color)
	local b = Instance.new("TextButton")
	b.Size = UDim2.new(0,260,0,40)
	b.Position = UDim2.new(0,20,0,y)
	b.Text = text
	b.BackgroundColor3 = color
	b.TextColor3 = Color3.new(1,1,1)
	b.Font = Enum.Font.GothamBold
	b.TextSize = 16
	b.Parent = x
	return b
end

local gB = makeBtn("IMORTAL: OFF", 60, Color3.fromRGB(60,0,0))
local sB = makeBtn("SPEED: OFF", 115, Color3.fromRGB(60,0,0))
local dB = makeBtn("DUPLICAR ITEM", 170, Color3.fromRGB(40,40,40))

--------------------------------------------------
-- ABRIR / FECHAR MENU (TOUCH FUNCIONAL)
--------------------------------------------------
ball.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.Touch
	or input.UserInputType == Enum.UserInputType.MouseButton1 then
		x.Visible = not x.Visible
	end
end)

--------------------------------------------------
-- ARRASTAR BOLINHA
--------------------------------------------------
do
	local dragging = false
	local startPos, startUI

	ball.InputBegan:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.Touch
		or i.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = true
			startPos = i.Position
			startUI = ball.Position
		end
	end)

	ball.InputEnded:Connect(function()
		dragging = false
	end)

	UIS.InputChanged:Connect(function(i)
		if dragging and (i.UserInputType == Enum.UserInputType.Touch
		or i.UserInputType == Enum.UserInputType.MouseMovement) then
			local delta = i.Position - startPos
			ball.Position = UDim2.new(
				startUI.X.Scale, startUI.X.Offset + delta.X,
				startUI.Y.Scale, startUI.Y.Offset + delta.Y
			)
		end
	end)
end

--------------------------------------------------
-- ARRASTAR MENU
--------------------------------------------------
do
	local dragging = false
	local startPos, startUI

	t.InputBegan:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.Touch
		or i.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = true
			startPos = i.Position
			startUI = x.Position
		end
	end)

	t.InputEnded:Connect(function()
		dragging = false
	end)

	UIS.InputChanged:Connect(function(i)
		if dragging and (i.UserInputType == Enum.UserInputType.Touch
		or i.UserInputType == Enum.UserInputType.MouseMovement) then
			local delta = i.Position - startPos
			x.Position = UDim2.new(
				startUI.X.Scale, startUI.X.Offset + delta.X,
				startUI.Y.Scale, startUI.Y.Offset + delta.Y
			)
		end
	end)
end

--------------------------------------------------
-- FUNÇÕES
--------------------------------------------------
local G, S = false, false
local hc
local normalSpeed, fastSpeed = 16, 50

local function char()
	return p.Character or p.CharacterAdded:Wait()
end

local function hum()
	return char():WaitForChild("Humanoid")
end

--------------------------------------------------
-- IMORTAL
--------------------------------------------------
gB.MouseButton1Click:Connect(function()
	local u = hum()
	G = not G

	if G then
		gB.Text = "IMORTAL: ON"
		gB.BackgroundColor3 = Color3.fromRGB(120,0,180)
		u.MaxHealth = math.huge
		u.Health = u.MaxHealth
		hc = u.HealthChanged:Connect(function()
			if G then u.Health = u.MaxHealth end
		end)
	else
		gB.Text = "IMORTAL: OFF"
		gB.BackgroundColor3 = Color3.fromRGB(60,0,0)
		u.MaxHealth = 100
		u.Health = 100
		if hc then hc:Disconnect() end
	end
end)

--------------------------------------------------
-- SPEED
--------------------------------------------------
sB.MouseButton1Click:Connect(function()
	local u = hum()
	S = not S

	if S then
		sB.Text = "SPEED: ON"
		sB.BackgroundColor3 = Color3.fromRGB(0,140,200)
		u.WalkSpeed = fastSpeed
	else
		sB.Text = "SPEED: OFF"
		sB.BackgroundColor3 = Color3.fromRGB(60,0,0)
		u.WalkSpeed = normalSpeed
	end
end)

--------------------------------------------------
-- DUPLICAR ITEM
--------------------------------------------------
dB.MouseButton1Click:Connect(function()
	local tool = char():FindFirstChildOfClass("Tool")
	if tool then
		tool:Clone().Parent = p.Backpack
		dB.Text = "DUPLICADO ✔"
	else
		dB.Text = "SEM ITEM ❌"
	end
	task.delay(0.8, function()
		dB.Text = "DUPLICAR ITEM"
	end)
end)

--------------------------------------------------
-- MINIMIZAR
--------------------------------------------------
local fechado = false
local tamanho = x.Size

min.MouseButton1Click:Connect(function()
	fechado = not fechado
	if fechado then
		min.Text = "+"
		for _,v in pairs(x:GetChildren()) do
			if v:IsA("TextButton") and v ~= min then
				v.Visible = false
			end
		end
		x.Size = UDim2.new(tamanho.X.Scale, tamanho.X.Offset, 0, 35)
	else
		min.Text = "-"
		for _,v in pairs(x:GetChildren()) do
			if v:IsA("TextButton") then
				v.Visible = true
			end
		end
		x.Size = tamanho
	end
end)

--------------------------------------------------
-- REAPLICAR AO MORRER
--------------------------------------------------
p.CharacterAdded:Connect(function()
	task.wait(0.5)
	local u = hum()
	if G then
		u.MaxHealth = math.huge
		u.Health = u.MaxHealth
	end
	if S then
		u.WalkSpeed = fastSpeed
	end
end)
