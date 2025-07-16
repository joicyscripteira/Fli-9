local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local hrp = character:WaitForChild("HumanoidRootPart")
local Workspace = game:GetService("Workspace")

-- Ponto de spawn
local spawnLocation = Workspace:FindFirstChild("SpawnLocation") or Workspace:FindFirstChild("Spawn")
if not spawnLocation then
    warn("Ponto de spawn não encontrado!")
    return
end

-- Criação da GUI
local gui = player:WaitForChild("PlayerGui"):FindFirstChild("FlyGui")
if not gui then
    gui = Instance.new("ScreenGui")
    gui.Name = "FlyGui"
    gui.ResetOnSpawn = false
    gui.Parent = player:WaitForChild("PlayerGui")
end

-- Botão
local flyButton = Instance.new("TextButton")
flyButton.Size = UDim2.new(0, 140, 0, 50)
flyButton.Position = UDim2.new(1, -150, 0.5, -25)
flyButton.Text = "Voar ao Spawn"
flyButton.BackgroundColor3 = Color3.fromRGB(30, 144, 255)
flyButton.TextColor3 = Color3.new(1, 1, 1)
flyButton.Font = Enum.Font.SourceSansBold
flyButton.TextSize = 20
flyButton.Parent = gui

-- Lógica de voo
local flying = false
local connection

flyButton.MouseButton1Click:Connect(function()
    if flying then return end
    flying = true

    local targetHeight = 60
    local targetYPos = spawnLocation.Position.Y + targetHeight

    connection = RunService.RenderStepped:Connect(function()
        if not character or not hrp then return end

        local currentPosition = hrp.Position
        local targetPosition = Vector3.new(spawnLocation.Position.X, targetYPos, spawnLocation.Position.Z)
        local direction = (targetPosition - currentPosition).Unit
        local distance = (targetPosition - currentPosition).Magnitude

        local speed = 60  -- studs por segundo
        local moveVector = direction * math.min(speed * RunService.RenderStepped:Wait(), distance)

        -- Move o personagem manualmente
        hrp.Velocity = Vector3.zero
        hrp.CFrame = hrp.CFrame + moveVector

        -- Finaliza voo se estiver perto
        if distance < 2 then
            flying = false
            connection:Disconnect()
        end
    end)
end)
