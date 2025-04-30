-- LocalScript - Fly com Verificação de Time

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

-- Nome do time permitido
local allowedTeam = "Testadores"

-- Interface
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "FlyGui"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

local button = Instance.new("TextButton")
button.Size = UDim2.new(0, 200, 0, 50)
button.Position = UDim2.new(0.5, -100, 0.85, 0)
button.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
button.TextColor3 = Color3.new(1, 1, 1)
button.Font = Enum.Font.SourceSansBold
button.TextSize = 24
button.Text = "Ativar Fly"
button.Parent = screenGui

-- Controle do fly
local flying = false
local flySpeed = 50
local bodyVelocity = nil
local connection = nil

local function startFlying()
	if not character or not humanoidRootPart then return end
	bodyVelocity = Instance.new("BodyVelocity")
	bodyVelocity.MaxForce = Vector3.new(1, 1, 1) * 1e5
	bodyVelocity.Velocity = Vector3.zero
	bodyVelocity.Parent = humanoidRootPart

	connection = RunService.RenderStepped:Connect(function()
		local direction = Vector3.zero
		local cam = workspace.CurrentCamera

		if UserInputService:IsKeyDown(Enum.KeyCode.W) then
			direction += cam.CFrame.LookVector
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.S) then
			direction -= cam.CFrame.LookVector
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.A) then
			direction -= cam.CFrame.RightVector
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.D) then
			direction += cam.CFrame.RightVector
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
			direction += Vector3.new(0, 1, 0)
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then
			direction -= Vector3.new(0, 1, 0)
		end

		if direction.Magnitude > 0 then
			bodyVelocity.Velocity = direction.Unit * flySpeed
		else
			bodyVelocity.Velocity = Vector3.zero
		end
	end)
end

local function stopFlying()
	if connection then connection:Disconnect() end
	if bodyVelocity then bodyVelocity:Destroy() end
end

-- Ação do botão
button.MouseButton1Click:Connect(function()
	if not player.Team or player.Team.Name ~= allowedTeam then
		button.Text = "Time Incorreto!"
		wait(1.5)
		button.Text = "Ativar Fly"
		return
	end

	flying = not flying
	if flying then
		startFlying()
		button.Text = "Desativar Fly"
	else
		stopFlying()
		button.Text = "Ativar Fly"
	end
end)

-- Garantir que funcione mesmo se o personagem for resetado
player.CharacterAdded:Connect(function(char)
	character = char
	humanoidRootPart = char:WaitForChild("HumanoidRootPart")
end)
