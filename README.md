-- ============================================
-- CONFIG
-- ============================================
local SCRIPT_ID = "b17a7d87"

-- ============================================
-- CARREGAR SDK (PROTEGIDO)
-- ============================================
local VexSDK = nil

pcall(function()
    VexSDK = loadstring(game:HttpGet("https://vexbin.lol/vexlytics/sdk/main.lua"))()
    VexSDK.script_id = SCRIPT_ID
end)

-- ============================================
-- SERVIÇOS
-- ============================================
local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local player = Players.LocalPlayer

-- ============================================
-- GUI
-- ============================================
local gui = Instance.new("ScreenGui")
gui.Name = "KeyUI"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

-- FRAME
local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 350, 0, 220)
frame.Position = UDim2.new(0.5, -175, 0.5, -110)
frame.BackgroundColor3 = Color3.fromRGB(25,25,35)
Instance.new("UICorner", frame)

-- TITLE
local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, -40, 0, 40)
title.Position = UDim2.new(0, 10, 0, 0)
title.Text = "🔐 Sistema de Key"
title.TextColor3 = Color3.new(1,1,1)
title.BackgroundTransparency = 1
title.Font = Enum.Font.GothamBold
title.TextSize = 16

-- CLOSE
local close = Instance.new("TextButton", frame)
close.Size = UDim2.new(0, 30, 0, 30)
close.Position = UDim2.new(1, -35, 0, 5)
close.Text = "X"
close.BackgroundColor3 = Color3.fromRGB(200,50,50)
close.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", close)

close.MouseButton1Click:Connect(function()
    gui:Destroy()
end)

-- BOX
local box = Instance.new("TextBox", frame)
box.Size = UDim2.new(0.8, 0, 0, 45)
box.Position = UDim2.new(0.1, 0, 0.35, 0)
box.PlaceholderText = "Digite sua key"
box.BackgroundColor3 = Color3.fromRGB(40,40,55)
box.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", box)

-- BUTTON
local btn = Instance.new("TextButton", frame)
btn.Size = UDim2.new(0.8, 0, 0, 50)
btn.Position = UDim2.new(0.1, 0, 0.6, 0)
btn.Text = "CONFIRMAR"
btn.BackgroundColor3 = Color3.fromRGB(0,120,220)
btn.TextColor3 = Color3.new(1,1,1)
btn.Font = Enum.Font.GothamBold
Instance.new("UICorner", btn)

-- STATUS
local status = Instance.new("TextLabel", frame)
status.Size = UDim2.new(1, 0, 0, 30)
status.Position = UDim2.new(0, 0, 1, -30)
status.BackgroundTransparency = 1
status.Text = ""
status.TextColor3 = Color3.fromRGB(255,100,100)

-- ============================================
-- DRAG
-- ============================================
local dragging = false
local dragStart, startPos

frame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = frame.Position
    end
end)

frame.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)

UIS.InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - dragStart
        frame.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
    end
end)

-- ============================================
-- VERIFICAR KEY
-- ============================================
btn.MouseButton1Click:Connect(function()
    local key = box.Text

    if key == "" then
        status.Text = "Digite uma key!"
        return
    end

    status.Text = "Verificando..."

    if not VexSDK then
        status.Text = "Erro API (HTTP bloqueado)"
        return
    end

    local result = nil
    local ok = pcall(function()
        result = VexSDK.check_key(key)
    end)

    if ok and result and result.code == "KEY_VALID" then
        status.Text = "KEY VÁLIDA!"
        status.TextColor3 = Color3.fromRGB(100,255,100)

        task.wait(1)
        gui:Destroy()

        print("ACESSO LIBERADO 🔥")

        -- SEU SCRIPT AQUI
        -- loadstring(game:HttpGet("SEU_LINK"))()

    else
        status.Text = "KEY INVÁLIDA!"
        status.TextColor3 = Color3.fromRGB(255,100,100)
    end
end)
