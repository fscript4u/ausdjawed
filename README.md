-- إنشاء العناصر الأساسية
local ScreenGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local UICorner = Instance.new("UICorner")
local ChangeSkinButton = Instance.new("TextButton")
local ChangeSizeButton = Instance.new("TextButton")
local ReButton = Instance.new("TextButton")
local NoteLabel = Instance.new("TextLabel")

-- إعداد ScreenGui
ScreenGui.Name = "FancyUI"
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false -- لا تختفي عند الموت

-- إعداد الإطار الرئيسي
MainFrame.Size = UDim2.new(0, 400, 0, 300)
MainFrame.Position = UDim2.new(0.5, -200, 0.5, -150)
MainFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
MainFrame.BorderSizePixel = 0
MainFrame.Parent = ScreenGui

-- إضافة الزوايا الدائرية
UICorner.CornerRadius = UDim.new(0, 15)
UICorner.Parent = MainFrame

-- جعل الإطار قابلاً للتحريك باستخدام الفأرة
local dragging = false
local dragInput, dragStart, startPos

MainFrame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = MainFrame.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

MainFrame.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        dragInput = input
    end
end)

game:GetService("UserInputService").InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        local delta = input.Position - dragStart
        MainFrame.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
    end
end)

-- إعداد زر تغيير السكن
ChangeSkinButton.Size = UDim2.new(0, 350, 0, 50)
ChangeSkinButton.Position = UDim2.new(0.5, -175, 0.2, 0)
ChangeSkinButton.BackgroundColor3 = Color3.fromRGB(20, 120, 200)
ChangeSkinButton.Text = "تغيير السكن سريع"
ChangeSkinButton.TextColor3 = Color3.new(1, 1, 1)
ChangeSkinButton.Font = Enum.Font.SourceSansBold
ChangeSkinButton.TextSize = 24
ChangeSkinButton.Parent = MainFrame

-- إعداد زر تغيير الحجم
ChangeSizeButton.Size = UDim2.new(0, 350, 0, 50)
ChangeSizeButton.Position = UDim2.new(0.5, -175, 0.4, 0)
ChangeSizeButton.BackgroundColor3 = Color3.fromRGB(200, 120, 20)
ChangeSizeButton.Text = "تغيير الحجم سريع"
ChangeSizeButton.TextColor3 = Color3.new(1, 1, 1)
ChangeSizeButton.Font = Enum.Font.SourceSansBold
ChangeSizeButton.TextSize = 24
ChangeSizeButton.Parent = MainFrame

-- إعداد زر re
ReButton.Size = UDim2.new(0, 350, 0, 50)
ReButton.Position = UDim2.new(0.5, -175, 0.6, 0)
ReButton.BackgroundColor3 = Color3.fromRGB(120, 20, 200)
ReButton.Text = "re"
ReButton.TextColor3 = Color3.new(1, 1, 1)
ReButton.Font = Enum.Font.SourceSansBold
ReButton.TextSize = 24
ReButton.Parent = MainFrame

-- إعداد الملاحظة
NoteLabel.Size = UDim2.new(0, 350, 0, 50)
NoteLabel.Position = UDim2.new(0.5, -175, 0.8, 0)
NoteLabel.BackgroundTransparency = 1
NoteLabel.Text = "مجاني لا داعي للادمن"
NoteLabel.TextColor3 = Color3.new(1, 1, 1)
NoteLabel.Font = Enum.Font.SourceSans
NoteLabel.TextSize = 20
NoteLabel.Parent = MainFrame

-- القوائم الكاملة
local skinArgsList = {
    {7224715700}, {7570856623}, {8169605275}, {7570856623}, {7224715700},
    {8169605275}, {7224715700}, {7570856623}, {8169605275}, {7570856623},
    {7224715700}, {8169605275}, {7224715700}, {7570856623}, {8169605275},
    {7570856623}, {7224715700}, {8169605275}, {7224715700}, {7570856623},
	{4833944032}, {7570856623}, {8169605275}, {7224715700}, {4833944032},
	{4833944032}, {7570856623}, {8169605275}, {7224715700}, {4833944032},
	{4833944032}, {7570856623}, {8169605275}, {7224715700}, {4833944032},
	{4833944032}, {7570856623}, {8169605275}, {7224715700}, {4833944032},
}
local sizeArgsList = {
    {0.5}, {0.6}, {0.7}, {0.8}, {0.9}, {1}, {1.1}, {1.2}, {1.3}, {1.4},
    {1.5}, {1.6}, {1.7}, {1.8}, {1.9}, {2.0}, {1.9}, {1.8}, {1.7}, {1.6},
    {1.5}, {1.4}, {1.3}, {1.2}, {1.1}, {0.9}, {0.8}, {0.7}, {0.6}, {0.5}
}

-- متغيرات التحكم
local isRunningSkin = false
local isRunningSize = false

-- دالة لتشغيل الطلبات
local function sendRequest(args, serviceName)
    game:GetService("ReplicatedStorage").PrivateCommands[serviceName]:FireServer(unpack(args))
end

-- دالة تغيير السكن
local function toggleSkin()
    isRunningSkin = not isRunningSkin
    ChangeSkinButton.Text = isRunningSkin and "إيقاف السكن" or "تغيير السكن سريع"
    if isRunningSkin then
        coroutine.wrap(function()
            while isRunningSkin do
                for _, args in ipairs(skinArgsList) do
                    if not isRunningSkin then break end -- الخروج الفوري عند الإيقاف
                    sendRequest(args, "Char")
                    task.wait(0.2)
                end
            end
        end)()
    end
end

-- دالة تغيير الحجم
local function toggleSize()
    isRunningSize = not isRunningSize
    ChangeSizeButton.Text = isRunningSize and "إيقاف الحجم" or "تغيير الحجم سريع"
    if isRunningSize then
        coroutine.wrap(function()
            while isRunningSize do
                for _, args in ipairs(sizeArgsList) do
                    if not isRunningSize then break end -- الخروج الفوري عند الإيقاف
                    sendRequest(args, "Size")
                    task.wait(0.01)
                end
            end
        end)()
    end
end

-- دالة زر re
local function activateRe()
    sendRequest({4592243049}, "Char")
    sendRequest({"No"}, "Char")
end

-- توصيل الوظائف بالأزرار
ChangeSkinButton.MouseButton1Click:Connect(toggleSkin)
ChangeSizeButton.MouseButton1Click:Connect(toggleSize)
ReButton.MouseButton1Click:Connect(activateRe)
