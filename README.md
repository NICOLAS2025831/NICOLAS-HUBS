-- Interface simples
local player = game.Players.LocalPlayer
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "FlyUI"

local flyButton = Instance.new("TextButton", gui)
flyButton.Size = UDim2.new(0, 150, 0, 50)
flyButton.Position = UDim2.new(0.5, -75, 0.9, -25)
flyButton.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
flyButton.TextColor3 = Color3.new(1, 1, 1)
flyButton.Font = Enum.Font.SourceSansBold
flyButton.TextSize = 24
flyButton.Text = "Ativar Fly"

-- Variáveis do fly
local flying = false
local flySpeed = 50
local UIS = game:GetService("UserInputService")
local runService = game:GetService("RunService")

-- Team Check (modifique para o nome do time correto)
local requiredTeam = "Testadores"

-- Função de fly
local function Fly()
	if not player.Character or not player.Character:FindFirstChild("HumanoidRootPart") then return end
	local hrp = player.Character.HumanoidRootPart
	local bv = Instance.new("BodyVelocity")
	bv.Velocity = Vector3.zero
	bv.MaxForce = Vector3.new(1, 1, 1) * math.huge
	bv.Parent = hrp

	local connection
	connection = runService.RenderStepped:Connect(function()
		local moveDirection = Vector3.zero
		if UIS:IsKeyDown(Enum.KeyCode.W) then moveDirection = moveDirection + workspace.CurrentCamera.CFrame.LookVector end
		if UIS:IsKeyDown(Enum.KeyCode.S) then moveDirection = moveDirection - workspace.CurrentCamera.CFrame.LookVector end
		if UIS:IsKeyDown(Enum.KeyCode.A) then moveDirection = moveDirection - workspace.CurrentCamera.CFrame.RightVector end
		if UIS:IsKeyDown(Enum.KeyCode.D) then moveDirection = moveDirection + workspace.CurrentCamera.CFrame.RightVector end
		if UIS:IsKeyDown(Enum.KeyCode.Space) then moveDirection = moveDirection + Vector3.new(0, 1, 0) end
		if UIS:IsKeyDown(Enum.KeyCode.LeftControl) then moveDirection = moveDirection - Vector3.new(0, 1, 0) end
		bv.Velocity = moveDirection.Unit * flySpeed
	end)

	return function()
		connection:Disconnect()
		bv:Destroy()
	end
end

-- Lógica do botão
local stopFly
flyButton.MouseButton1Click:Connect(function()
	if player.Team and player.Team.Name ~= requiredTeam then
		flyButton.Text = "Acesso Negado"
		task.wait(1)
		flyButton.Text = "Ativar Fly"
		return
	end

	flying = not flying
	if flying then
		stopFly = Fly()
		flyButton.Text = "Desativar Fly"
	else
		if stopFly then stopFly() end
		flyButton.Text = "Ativar Fly"
	end
end)
