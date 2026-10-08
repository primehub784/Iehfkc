--[[
    ╔══════════════════════════════════════╗
    ║           P R I M E   H U B          ║
    ║   v1.4 — SHIFTLOCK EDITION           ║
    ║   ✦ Câmera trava ao gravar/rodar     ║
    ╚══════════════════════════════════════╝
]]

local Players          = game:GetService("Players")
local RunService       = game:GetService("RunService")
local TweenService     = game:GetService("TweenService")
local CoreGui          = game:GetService("CoreGui")
local HttpService      = game:GetService("HttpService")
local UserInputService = game:GetService("UserInputService")

local LocalPlayer = Players.LocalPlayer

-- ⚙️ CONFIG
local RECORD_HZ       = 120
local RECORD_INTERVAL = 1 / RECORD_HZ
local RECORD_MIN_DIST = 0.08
local MAX_FRAMES      = 2400
local LINE_MIN_DIST   = 0.3
local SAVE_FOLDER     = "PrimeHub_Saves"

-- 🎥 SHIFTLOCK CONFIG
local SHIFT_CAM_DIST  = 12
local SHIFT_CAM_HEIGHT= 2.5
local SHIFT_LOOK_AHEAD= 5

-- 🎬 Animações
local ANIM_IDLE     = "rbxassetid://180435571"
local ANIM_WALK_FWD = "rbxassetid://134324036420171"
local ANIM_JUMP     = "rbxassetid://125750702"
local ANIM_FALL     = "rbxassetid://180436148"

-- 🌐 PARKOURS GLOBAIS
local PARKOURS_GLOBAIS = {
    -- Cole os dados aqui quando tiver
}

-- 📋 COLAS TAF
local COLAS = {
    { nome="🐕 TAF (CIGS)", cor=Color3.fromRGB(240,180,40), itens={
        "🐕 TAF: Teste de Aptidão Física: CIGS",
        "🎖 Comandante: Sagas",
        "🎖 Subcomandante: Deselegant",
        "📜 Lema: Treinar para resistir & Combater para vencer.",
        "🗣 Pronomes: Saudações, senhor Guerreiro de Selva. / Saudações, senhores Guerreiros de Selva.",
        "🌿 Início: Retirar boina, dar saudações ao instrutor e passar pelos escudos.",
    }},
    { nome="🥷 CIE", cor=Color3.fromRGB(70,130,200), itens={
        "🥷 CIE: Agente.",
        "👤 Criador: vicofjgfhf",
        "👤 Sub criador: RIP_dabfj8w",
        "🎖 Comandante: eriqurrr.",
        "🎖 Subcomandante: RodrigoPao8",
        "🗣 Saudações: Saudações, senhores Agentes. / Saudações, senhores Fantasmas. / Saudações, senhor Agente. / Saudações, senhor Fantasma.",
        "📜 Lema CIE: Inteligência para Vitória & Saber para Prever.",
    }},
    { nome="🐎 REC MEC", cor=Color3.fromRGB(240,180,40), itens={
        "🐎 REC MEC: Cavaleiros.",
        "👤 Comandante: terra_2433.",
        "🎖 Subcomandante: Contanum5bl",
        "📜 Lema: Haverá sempre uma Cavalaria! Aço na mente, motor no peito e honra na missão!",
        "🗣 Saudações: Saudações, senhores Cavaleiros. / Saudações, senhor Cavaleiro.",
    }},
    { nome="👮 Polícia", cor=Color3.fromRGB(70,200,130), itens={
        "🎖 Subcomandante: Matheuslindo587.",
        "📜 Lema: Orientar o Responsável, Corrigir o Irresponsável, Prender o Incorrigível.",
        "🗣 Pronomes / Saudações: Saudações, senhores Policiais. / Saudações, senhor Policial.",
    }},
    { nome="🥷 BFE (Fantasma)", cor=Color3.fromRGB(200,60,60), itens={
        "🥷 BFE: Fantasma.",
        "👤 Criador: NATANHMELLO4.",
        "📅 Criado: 1983.",
        "🛡 Escudo: O escudo do BFE possui fundo preto com bordas amarelas. No centro, há um paraquedas branco junto de uma faca vermelha, simbolizando operações especiais e combate. Na parte inferior, aparece a faixa de Forças Especiais.",
        "👤 Comandante: RenanFoxiy.",
        "🎖 Subcomandante: TILAPIA_PROFISSIONAL.",
        "📜 Lema: Qualquer missão, em qualquer lugar, a qualquer hora, de qualquer maneira.",
        "🗣 Saudações: Saudações, senhores Fantasmas. / Saudações, senhor Fantasma.",
        "📋 Licença: Com licença, senhores Fantasmas. / Com licença, senhor Fantasma.",
    }},
    { nome="🛡 BPE", cor=Color3.fromRGB(70,200,130), itens={
        "🛡 BPE: Polícia do Exército",
        "👤 Comandante: zCostasz.",
    }},
    { nome="🎒 BIP", cor=Color3.fromRGB(180,190,80), itens={
        "🎒 BIP: Batalhão de Infantaria Paraquedista.",
        "🎖 Subcomandante:",
        "📜 Lema: Paraquedistas, sempre prontos para a missão, do céu ao chão.",
        "🎯 Missão: Manter a tropa pronta para atuar em missões aeroterrestres, com disciplina, coragem e prontidão.",
        "🗣 Saudações: Saudações, senhores Paraquedistas. / Saudações, senhor Paraquedista.",
        "📋 Licença: Com licença, senhores Paraquedistas. / Com licença, senhor Paraquedista.",
        "📣 Grito de Guerra: PARAQUEDISTA!",
    }},
    { nome="💀 BAC", cor=Color3.fromRGB(200,100,40), itens={
        "💀 INFORMAÇÕES BAC: Batalhão de Ações de Comandos",
        "👑 Dono: MateusHgz",
        "👤 Comandante: SasukePro202.",
        "🎖 Subcomandante: DanielSxS2.",
        "📜 Lema da BAC: O máximo de confusão, morte e destruição na retaguarda do inimigo.",
        "🗣 Saudações: Saudações, senhor Comando. / Saudações, senhores Comandos.",
    }},
    { nome="💻 CYBER", cor=Color3.fromRGB(150,100,220), itens={
        "💻 CYBER: Comando de Defesa Cibernética.",
        "🗣 Saudações: Saudações, senhores Analistas.",
        "👤 Criador: wAnTee16j5156.",
        "👑 Dono: MaxTheJp1. / ItsMeLyrio. / Gabriel2444q.",
        "👤 Comandante: highanddry98",
        "🎖 Subcomandante: Não tem.",
        "📜 Lema: Segurança no ciberespaço, soberania para a Nação.",
        "🛡 JURAMENTO: JURO GUARDAR SIGILO SOBRE TUDO QUE VER E OUVRIR NO COMDCIBER!",
    }},
    { nome="🌵 CAATINGA", cor=Color3.fromRGB(80,200,100), itens={
        "🌵 CAATINGA: Guardiões da Caatinga.",
        "👤 Comandante: Gabrielcm04",
        "🎖 Subcomandante: Plk_Ln17",
        "📜 Lema: O pai cria, a mãe educa e a Caatinga elimina.",
    }},
}

local THEME = {
    bg=Color3.fromRGB(8,6,16), card=Color3.fromRGB(16,12,26),
    cardSoft=Color3.fromRGB(24,18,40), border=Color3.fromRGB(60,40,100),
    purple=Color3.fromRGB(160,80,255), purpleDark=Color3.fromRGB(90,40,180),
    purpleLite=Color3.fromRGB(200,140,255), purpleNeon=Color3.fromRGB(180,100,255),
    red=Color3.fromRGB(255,60,120), redSoft=Color3.fromRGB(70,20,50),
    green=Color3.fromRGB(60,220,160), greenSoft=Color3.fromRGB(20,60,45),
    text=Color3.fromRGB(245,240,255), textDim=Color3.fromRGB(170,155,200),
    textMuted=Color3.fromRGB(120,105,150),
}

local function ensureFolder()
    if type(isfolder)~="function" or type(makefolder)~="function" then return end
    pcall(function() if not isfolder(SAVE_FOLDER) then makefolder(SAVE_FOLDER) end end)
end
local hasFS = (type(writefile)=="function") and (type(readfile)=="function")
local function sanitize(n) local s=n:gsub("[^%w_%- ]",""):gsub("%s+","_"); return s~="" and s or "trajetoria" end
local function saveToDisk(n,d)
    if not hasFS then return false end
    ensureFolder()
    return pcall(function() writefile(SAVE_FOLDER.."/"..sanitize(n)..".json", HttpService:JSONEncode(d)) end)
end
local function deleteFromDisk(n)
    if not hasFS or type(delfile)~="function" then return end
    pcall(delfile, SAVE_FOLDER.."/"..sanitize(n)..".json")
end

if CoreGui:FindFirstChild("PrimeHubUI") then CoreGui.PrimeHubUI:Destroy() end
for _,o in ipairs(workspace:GetChildren()) do if o.Name=="PrimeHubLine" then o:Destroy() end end

-- ⭐ FORWARD DECLARATIONS
local playFrames
local stopPlayback
local prepareAnimations
local stopWalkAnim
local switchAnim
local enableShiftLock
local disableShiftLock

local S = {
    recording=false, playing=false, frames=table.create(MAX_FRAMES),
    recordStart=0, playConn=nil, recordConn=nil,
    char=nil, hrp=nil, humanoid=nil, trail=nil, lineFolder=nil, saves={},
    animTracks=nil, animCurrent=nil, lastAnimSpeed=0, lastPlayCF=nil,
    minimized=false, smoothLook=nil, rigType="R6",
    -- ShiftLock
    shiftLockConn=nil, shiftLockActive=false,
    prevCameraType=nil, prevMouseBehavior=nil, prevMouseIcon=nil,
}

local function refreshChar()
    local c=LocalPlayer.Character; if not c then return false end
    local h=c:FindFirstChild("HumanoidRootPart"); if not h then return false end
    S.char=c; S.hrp=h; S.humanoid=c:FindFirstChildOfClass("Humanoid"); return true
end
local function notify(m)
    pcall(function() game:GetService("StarterGui"):SetCore("SendNotification",{Title="PRIME HUB",Text=m,Duration=2}) end)
end

-- ⭐ SHIFT LOCK FUNCTIONS
enableShiftLock = function()
    if S.shiftLockActive then return end
    S.shiftLockActive = true

    local cam = workspace.CurrentCamera
    if not cam then return end

    S.prevCameraType = cam.CameraType
    S.prevMouseBehavior = UserInputService.MouseBehavior
    S.prevMouseIcon = UserInputService.MouseIconEnabled

    pcall(function()
        UserInputService.MouseBehavior = Enum.MouseBehavior.LockCenter
        UserInputService.MouseIconEnabled = false
        cam.CameraType = Enum.CameraType.Scriptable
    end)

    S.shiftLockConn = RunService.RenderStepped:Connect(function()
        local c = workspace.CurrentCamera
        local hrp = S.hrp
        if not c or not hrp then return end

        local pos = hrp.Position + Vector3.new(0, SHIFT_CAM_HEIGHT, 0)
        local look = hrp.CFrame.LookVector
        look = Vector3.new(look.X, 0, look.Z)
        if look.Magnitude < 0.01 then look = Vector3.new(0, 0, -1) end
        look = look.Unit

        local camPos = pos - look * SHIFT_CAM_DIST
        c.CFrame = CFrame.lookAt(camPos, pos + look * SHIFT_LOOK_AHEAD)
    end)
end

disableShiftLock = function()
    if not S.shiftLockActive then return end
    S.shiftLockActive = false

    if S.shiftLockConn then
        S.shiftLockConn:Disconnect()
        S.shiftLockConn = nil
    end

    pcall(function()
        UserInputService.MouseBehavior = S.prevMouseBehavior or Enum.MouseBehavior.Default
        UserInputService.MouseIconEnabled = (S.prevMouseIcon ~= false)
        local cam = workspace.CurrentCamera
        if cam then
            cam.CameraType = S.prevCameraType or Enum.CameraType.Custom
        end
    end)
end

local function createTrail()
    if not refreshChar() then return end
    local hrp=S.hrp
    local old=hrp:FindFirstChild("PrimeHubTrail"); if old then old:Destroy() end
    local a0=Instance.new("Attachment",hrp); a0.Position=Vector3.new(0,0.5,0)
    local a1=Instance.new("Attachment",hrp); a1.Position=Vector3.new(0,-0.5,0)
    local t=Instance.new("Trail")
    t.Name="PrimeHubTrail"; t.Attachment0=a0; t.Attachment1=a1
    t.Lifetime=3; t.MinLength=0; t.FaceCamera=true
    t.Color=ColorSequence.new(THEME.purple)
    t.Transparency=NumberSequence.new({NumberSequenceKeypoint.new(0,0.1),NumberSequenceKeypoint.new(1,1)})
    t.WidthScale=NumberSequence.new({NumberSequenceKeypoint.new(0,1),NumberSequenceKeypoint.new(1,0)})
    t.Parent=hrp; S.trail=t
end
local function destroyTrail()
    if S.trail and S.trail.Parent then
        S.trail.Enabled=false; local t=S.trail
        task.delay(3.2,function() if t and t.Parent then t.Parent:Destroy() end end)
    end
    S.trail=nil
end

local function clearLine()
    if S.lineFolder and S.lineFolder.Parent then S.lineFolder:Destroy() end
    S.lineFolder=nil
end
local function drawLine(cfs)
    clearLine(); if #cfs<2 then return end
    local pts={cfs[1].Position}
    for i=2,#cfs do local p=cfs[i].Position if (p-pts[#pts]).Magnitude>LINE_MIN_DIST then pts[#pts+1]=p end end
    local lp=cfs[#cfs].Position
    if (lp-pts[#pts]).Magnitude>0.1 then pts[#pts+1]=lp end
    if #pts<2 then return end
    local f=Instance.new("Folder",workspace); f.Name="PrimeHubLine"
    for i=1,#pts-1 do
        local a,b=pts[i],pts[i+1]; local d=(b-a).Magnitude
        local s=Instance.new("Part")
        s.Anchored=true; s.CanCollide=false; s.CanQuery=false; s.CanTouch=false; s.CastShadow=false
        s.Material=Enum.Material.Neon; s.Color=THEME.purple
        s.Size=Vector3.new(0.15,0.15,d); s.CFrame=CFrame.lookAt((a+b)*0.5,b); s.Parent=f
    end
    S.lineFolder=f
end

-- ============ UI ============
local gui = Instance.new("ScreenGui")
gui.Name="PrimeHubUI"; gui.ResetOnSpawn=false; gui.IgnoreGuiInset=true
gui.ZIndexBehavior=Enum.ZIndexBehavior.Sibling; gui.DisplayOrder=999; gui.Parent=CoreGui

local floatBtn = Instance.new("TextButton", gui)
floatBtn.Name = "FloatBtn"
floatBtn.Size = UDim2.new(0, 52, 0, 52)
floatBtn.Position = UDim2.new(0, 20, 0.5, -26)
floatBtn.BackgroundColor3 = THEME.purple
floatBtn.BorderSizePixel = 0
floatBtn.Text = "PH"
floatBtn.Font = Enum.Font.GothamBlack
floatBtn.TextSize = 18
floatBtn.TextColor3 = THEME.text
floatBtn.AutoButtonColor = false
floatBtn.ZIndex = 200
Instance.new("UICorner", floatBtn).CornerRadius = UDim.new(1, 0)

local floatStroke = Instance.new("UIStroke", floatBtn)
floatStroke.Color = THEME.purpleNeon
floatStroke.Thickness = 2
floatStroke.Transparency = 0

local floatGrad = Instance.new("UIGradient", floatBtn)
floatGrad.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, THEME.purpleDark),
    ColorSequenceKeypoint.new(0.5, THEME.purple),
    ColorSequenceKeypoint.new(1, THEME.purpleNeon),
})

task.spawn(function()
    local a = 0
    while floatBtn.Parent do
        a = (a + 2) % 360
        floatGrad.Rotation = a
        task.wait(0.05)
    end
end)

local main = Instance.new("Frame", gui)
main.Name = "Main"
main.Size=UDim2.new(0,320,0,500); main.Position=UDim2.new(0.5,-160,0.5,-250)
main.BackgroundColor3=THEME.card; main.BorderSizePixel=0
main.Active=true; main.Draggable=true; main.Visible=true
Instance.new("UICorner",main).CornerRadius=UDim.new(0,24)

local mainStroke=Instance.new("UIStroke",main)
mainStroke.Color=THEME.purple; mainStroke.Thickness=1.5; mainStroke.Transparency=0.4

local function minimize()
    if S.minimized then return end
    S.minimized = true
    TweenService:Create(main, TweenInfo.new(0.35, Enum.EasingStyle.Back, Enum.EasingDirection.In), {
        Size = UDim2.new(0, 0, 0, 0),
        Position = UDim2.new(0.5, 0, 0.5, 0),
    }):Play()
    task.delay(0.36, function() main.Visible = false end)
end

local function maximize()
    if not S.minimized then return end
    S.minimized = false
    main.Visible = true
    main.Size = UDim2.new(0, 0, 0, 0)
    main.Position = UDim2.new(0.5, 0, 0.5, 0)
    TweenService:Create(main, TweenInfo.new(0.5, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
        Size = UDim2.new(0, 320, 0, 500),
        Position = UDim2.new(0.5, -160, 0.5, -250),
    }):Play()
end

floatBtn.MouseButton1Click:Connect(function()
    if S.minimized then maximize() else minimize() end
end)

local header=Instance.new("Frame",main)
header.Size=UDim2.new(1,0,0,90); header.BackgroundColor3=THEME.purple
header.BorderSizePixel=0; header.ZIndex=1
Instance.new("UICorner",header).CornerRadius=UDim.new(0,24)

local headerGrad=Instance.new("UIGradient",header)
headerGrad.Color=ColorSequence.new({
    ColorSequenceKeypoint.new(0,THEME.purpleDark),
    ColorSequenceKeypoint.new(0.5,THEME.purple),
    ColorSequenceKeypoint.new(1,THEME.purpleNeon),
})

local headerFade=Instance.new("Frame",header)
headerFade.Size=UDim2.new(1,0,0,40); headerFade.Position=UDim2.new(0,0,1,-40)
headerFade.BackgroundColor3=THEME.card; headerFade.BackgroundTransparency=0.5
headerFade.BorderSizePixel=0; headerFade.ZIndex=2

local logoWrap=Instance.new("Frame",main)
logoWrap.Size=UDim2.new(0,62,0,62); logoWrap.Position=UDim2.new(0,16,0,14)
logoWrap.BackgroundColor3=THEME.bg; logoWrap.BorderSizePixel=0; logoWrap.ZIndex=10
Instance.new("UICorner",logoWrap).CornerRadius=UDim.new(0,16)
local logoStroke=Instance.new("UIStroke",logoWrap)
logoStroke.Color=THEME.purpleNeon; logoStroke.Thickness=2; logoStroke.Transparency=0

local crown=Instance.new("TextLabel",logoWrap)
crown.Size=UDim2.new(1,0,0,22); crown.Position=UDim2.new(0,0,0,2)
crown.BackgroundTransparency=1; crown.Text="♛"; crown.Font=Enum.Font.GothamBlack
crown.TextSize=22; crown.TextColor3=THEME.purpleNeon; crown.ZIndex=11

local logoText=Instance.new("TextLabel",logoWrap)
logoText.Size=UDim2.new(1,0,0,28); logoText.Position=UDim2.new(0,0,0,26)
logoText.BackgroundTransparency=1; logoText.Text="PH"; logoText.Font=Enum.Font.GothamBlack
logoText.TextSize=22; logoText.TextColor3=THEME.text; logoText.ZIndex=11

local title=Instance.new("TextLabel",main)
title.Size=UDim2.new(1,-100,0,22); title.Position=UDim2.new(0,88,0,22)
title.BackgroundTransparency=1; title.Text="PRIME HUB"; title.Font=Enum.Font.GothamBlack
title.TextSize=20; title.TextColor3=THEME.text; title.TextXAlignment=Enum.TextXAlignment.Left; title.ZIndex=10

local subtitle=Instance.new("TextLabel",main)
subtitle.Size=UDim2.new(1,-100,0,14); subtitle.Position=UDim2.new(0,88,0,46)
subtitle.BackgroundTransparency=1; subtitle.Text="ShiftLock Edition  •  v1.4"
subtitle.Font=Enum.Font.Gotham; subtitle.TextSize=11; subtitle.TextColor3=THEME.textDim
subtitle.TextXAlignment=Enum.TextXAlignment.Left; subtitle.ZIndex=10

local statusBadge=Instance.new("Frame",main)
statusBadge.Size=UDim2.new(0,104,0,28); statusBadge.Position=UDim2.new(1,-122,0,62)
statusBadge.BackgroundColor3=THEME.cardSoft; statusBadge.BorderSizePixel=0; statusBadge.ZIndex=10
Instance.new("UICorner",statusBadge).CornerRadius=UDim.new(1,0)
local statusGlow=Instance.new("UIStroke",statusBadge)
statusGlow.Color=THEME.purple; statusGlow.Thickness=1; statusGlow.Transparency=0.3
local statusDot=Instance.new("Frame",statusBadge)
statusDot.Size=UDim2.new(0,8,0,8); statusDot.Position=UDim2.new(0,12,0.5,-4)
statusDot.BackgroundColor3=THEME.purple; statusDot.BorderSizePixel=0; statusDot.ZIndex=11
Instance.new("UICorner",statusDot).CornerRadius=UDim.new(1,0)

local statusText=Instance.new("TextLabel",statusBadge)
statusText.Size=UDim2.new(1,-30,1,0); statusText.Position=UDim2.new(0,26,0,0)
statusText.BackgroundTransparency=1; statusText.Text="Pronto"
statusText.Font=Enum.Font.GothamBold; statusText.TextSize=11
statusText.TextColor3=THEME.purple; statusText.TextXAlignment=Enum.TextXAlignment.Left; statusText.ZIndex=11

local btnClose=Instance.new("TextButton",main)
btnClose.Size=UDim2.new(0,26,0,26); btnClose.Position=UDim2.new(1,-38,0,16)
btnClose.BackgroundColor3=THEME.cardSoft; btnClose.BorderSizePixel=0
btnClose.Text="—"; btnClose.Font=Enum.Font.GothamBold; btnClose.TextSize=14
btnClose.TextColor3=THEME.textDim; btnClose.AutoButtonColor=false; btnClose.ZIndex=10
Instance.new("UICorner",btnClose).CornerRadius=UDim.new(1,0)

local divider=Instance.new("Frame",main)
divider.Size=UDim2.new(1,-36,0,1); divider.Position=UDim2.new(0,18,0,102)
divider.BackgroundColor3=THEME.border; divider.BorderSizePixel=0; divider.ZIndex=5

local infoLabel=Instance.new("TextLabel",main)
infoLabel.Size=UDim2.new(1,-36,0,18); infoLabel.Position=UDim2.new(0,18,0,112)
infoLabel.BackgroundTransparency=1; infoLabel.Text="0 pontos gravados"
infoLabel.Font=Enum.Font.Gotham; infoLabel.TextSize=11
infoLabel.TextColor3=THEME.textMuted; infoLabel.TextXAlignment=Enum.TextXAlignment.Left; infoLabel.ZIndex=5

local function makeButton(text,pos,size,color,textColor,icon)
    local btn=Instance.new("TextButton")
    btn.Size=size or UDim2.new(1,-36,0,46); btn.Position=pos
    btn.BackgroundColor3=color; btn.BorderSizePixel=0
    btn.Text=""; btn.AutoButtonColor=false; btn.ZIndex=5
    btn.ClipsDescendants=true; btn.Parent=main
    Instance.new("UICorner",btn).CornerRadius=UDim.new(0,14)

    local stroke=Instance.new("UIStroke",btn)
    stroke.Color=color:Lerp(Color3.new(1,1,1),0.4); stroke.Thickness=0; stroke.Transparency=0.4

    local iconLbl=Instance.new("TextLabel",btn)
    iconLbl.Size=UDim2.new(0,26,1,0); iconLbl.Position=UDim2.new(0,16,0,0)
    iconLbl.BackgroundTransparency=1; iconLbl.Text=icon or ""
    iconLbl.Font=Enum.Font.GothamBold; iconLbl.TextSize=15
    iconLbl.TextColor3=textColor or THEME.text; iconLbl.ZIndex=6

    local textLbl=Instance.new("TextLabel",btn)
    textLbl.Size=UDim2.new(1,-50,1,0); textLbl.Position=UDim2.new(0,44,0,0)
    textLbl.BackgroundTransparency=1; textLbl.Text=text
    textLbl.Font=Enum.Font.GothamBold; textLbl.TextSize=14
    textLbl.TextColor3=textColor or THEME.text; textLbl.TextXAlignment=Enum.TextXAlignment.Left; textLbl.ZIndex=6

    btn.MouseEnter:Connect(function()
        TweenService:Create(btn,TweenInfo.new(0.2),{BackgroundColor3=color:Lerp(Color3.new(1,1,1),0.18)}):Play()
        TweenService:Create(stroke,TweenInfo.new(0.2),{Thickness=1.5}):Play()
    end)
    btn.MouseLeave:Connect(function()
        TweenService:Create(btn,TweenInfo.new(0.25),{BackgroundColor3=color}):Play()
        TweenService:Create(stroke,TweenInfo.new(0.25),{Thickness=0}):Play()
    end)
    return btn,textLbl,iconLbl,stroke,color
end

local btnRecord,txtRecord,iconRecord,_,colorRecord =
    makeButton("Gravar",UDim2.new(0,18,0,142),UDim2.new(1,-36,0,44),THEME.purple,THEME.text,"⏺")
local btnPlay,txtPlay,iconPlay,_,colorPlay =
    makeButton("Reproduzir",UDim2.new(0,18,0,194),UDim2.new(1,-36,0,44),THEME.purple,THEME.text,"▶")
local btnStopPlay =
    makeButton("Parar Reprodução",UDim2.new(0,18,0,246),UDim2.new(1,-36,0,42),THEME.cardSoft,THEME.textDim,"⏹")
local btnSave =
    makeButton("Salvar",UDim2.new(0,18,0,298),UDim2.new(0.5,-22,0,42),THEME.cardSoft,THEME.textDim,"💾")
local btnClear =
    makeButton("Apagar",UDim2.new(0.5,4,0,298),UDim2.new(0.5,-22,0,42),THEME.cardSoft,THEME.textDim,"🗑")
local btnLibrary =
    makeButton("Biblioteca",UDim2.new(0,18,0,350),UDim2.new(0.34,-14,0,42),THEME.purpleDark,THEME.purpleLite,"📚")
local btnColas =
    makeButton("Colas",UDim2.new(0.34,0,0,350),UDim2.new(0.33,-10,0,42),THEME.cardSoft,THEME.textDim,"🗒")
local btnParkour =
    makeButton("Parkour",UDim2.new(0.67,0,0,350),UDim2.new(0.33,-9,0,42),THEME.cardSoft,THEME.textDim,"🕵️")

local footer=Instance.new("TextLabel",main)
footer.Size=UDim2.new(1,-36,0,16); footer.Position=UDim2.new(0,18,1,-26)
footer.BackgroundTransparency=1; footer.Text="PRIME HUB © 2026  •  v1.4"
footer.Font=Enum.Font.Gotham; footer.TextSize=9
footer.TextColor3=THEME.textMuted; footer.TextXAlignment=Enum.TextXAlignment.Center; footer.ZIndex=5

local function setStatus(s)
    if s=="ready" then
        statusBadge.BackgroundColor3=THEME.cardSoft
        statusText.Text="Pronto"; statusText.TextColor3=THEME.purple
    elseif s=="recording" then
        statusBadge.BackgroundColor3=THEME.redSoft
        statusText.Text="Gravando..."; statusText.TextColor3=THEME.red
    elseif s=="playing" then
        statusBadge.BackgroundColor3=THEME.greenSoft
        statusText.Text="Reproduzindo..."; statusText.TextColor3=THEME.green
    end
end

local function updateInfo()
    infoLabel.Text = tostring(#S.frames) .. " pontos gravados"
end

local overlay=Instance.new("TextButton",gui)
overlay.Size=UDim2.new(1,0,1,0); overlay.BackgroundColor3=Color3.new(0,0,0)
overlay.BackgroundTransparency=1; overlay.BorderSizePixel=0; overlay.Text=""
overlay.AutoButtonColor=false; overlay.Visible=false; overlay.ZIndex=99

-- MODAL SALVAR
local modal=Instance.new("Frame",gui)
modal.Size=UDim2.new(0,300,0,200); modal.Position=UDim2.new(0.5,-150,0.5,-100)
modal.BackgroundColor3=THEME.card; modal.BorderSizePixel=0; modal.Visible=false; modal.ZIndex=100
Instance.new("UICorner",modal).CornerRadius=UDim.new(0,20)
local modalStroke=Instance.new("UIStroke",modal)
modalStroke.Color=THEME.purple; modalStroke.Thickness=1.5; modalStroke.Transparency=0.3

local modalHeader=Instance.new("Frame",modal)
modalHeader.Size=UDim2.new(1,0,0,46); modalHeader.BackgroundColor3=THEME.purple
modalHeader.BorderSizePixel=0; modalHeader.ZIndex=101
Instance.new("UICorner",modalHeader).CornerRadius=UDim.new(0,20)

local modalTitle=Instance.new("TextLabel",modal)
modalTitle.Size=UDim2.new(1,-60,0,26); modalTitle.Position=UDim2.new(0,20,0,10)
modalTitle.BackgroundTransparency=1; modalTitle.Text="💾  Salvar Trajetória"
modalTitle.Font=Enum.Font.GothamBold; modalTitle.TextSize=14
modalTitle.TextColor3=THEME.text; modalTitle.TextXAlignment=Enum.TextXAlignment.Left; modalTitle.ZIndex=103

local inputBox=Instance.new("TextBox",modal)
inputBox.Size=UDim2.new(1,-32,0,40); inputBox.Position=UDim2.new(0,16,0,80)
inputBox.BackgroundColor3=THEME.cardSoft; inputBox.BorderSizePixel=0
inputBox.Text=""; inputBox.PlaceholderText="ex: TORRE 1"
inputBox.PlaceholderColor3=THEME.textMuted; inputBox.Font=Enum.Font.Gotham
inputBox.TextSize=13; inputBox.TextColor3=THEME.text
inputBox.TextXAlignment=Enum.TextXAlignment.Left; inputBox.ClearTextOnFocus=false; inputBox.ZIndex=102
Instance.new("UICorner",inputBox).CornerRadius=UDim.new(0,10)
local inPad=Instance.new("UIPadding",inputBox)
inPad.PaddingLeft=UDim.new(0,12); inPad.PaddingRight=UDim.new(0,12)

local function makeModalBtn(text,pos,color,textColor,parent)
    local b=Instance.new("TextButton",parent)
    b.Size=UDim2.new(0.5,-22,0,38); b.Position=pos
    b.BackgroundColor3=color; b.BorderSizePixel=0; b.Text=text
    b.Font=Enum.Font.GothamBold; b.TextSize=13; b.TextColor3=textColor or THEME.text
    b.AutoButtonColor=false; b.ZIndex=102
    Instance.new("UICorner",b).CornerRadius=UDim.new(0,10)
    return b
end

local btnCancelModal=makeModalBtn("Cancelar",UDim2.new(0,16,1,-52),THEME.cardSoft,THEME.textDim,modal)
local btnConfirmModal=makeModalBtn("Salvar",UDim2.new(0.5,6,1,-52),THEME.purple,THEME.text,modal)

local function openModal()
    modal.Visible=true; overlay.Visible=true; inputBox.Text=""
    overlay.BackgroundTransparency=0.5
    task.wait(0.1); inputBox:CaptureFocus()
end
local function closeModal()
    modal.Visible=false; overlay.Visible=false; overlay.BackgroundTransparency=1
end

-- BIBLIOTECA
local libModal=Instance.new("Frame",gui)
libModal.Size=UDim2.new(0,340,0,420); libModal.Position=UDim2.new(0.5,-170,0.5,-210)
libModal.BackgroundColor3=THEME.card; libModal.BorderSizePixel=0
libModal.Visible=false; libModal.ZIndex=100
Instance.new("UICorner",libModal).CornerRadius=UDim.new(0,20)
local libStroke=Instance.new("UIStroke",libModal)
libStroke.Color=THEME.purple; libStroke.Thickness=1.5; libStroke.Transparency=0.3

local libHeader=Instance.new("Frame",libModal)
libHeader.Size=UDim2.new(1,0,0,50); libHeader.BackgroundColor3=THEME.purple
libHeader.BorderSizePixel=0; libHeader.ZIndex=101
Instance.new("UICorner",libHeader).CornerRadius=UDim.new(0,20)

local libTitle=Instance.new("TextLabel",libModal)
libTitle.Size=UDim2.new(1,-180,0,26); libTitle.Position=UDim2.new(0,20,0,12)
libTitle.BackgroundTransparency=1; libTitle.Text="📚  Minha Biblioteca"
libTitle.Font=Enum.Font.GothamBold; libTitle.TextSize=15
libTitle.TextColor3=THEME.text; libTitle.TextXAlignment=Enum.TextXAlignment.Left; libTitle.ZIndex=103

local libCount=Instance.new("TextLabel",libModal)
libCount.Size=UDim2.new(0,100,0,26); libCount.Position=UDim2.new(1,-140,0,12)
libCount.BackgroundTransparency=1; libCount.Text="0 itens"
libCount.Font=Enum.Font.Gotham; libCount.TextSize=11
libCount.TextColor3=THEME.textDim; libCount.TextXAlignment=Enum.TextXAlignment.Right; libCount.ZIndex=103

local btnRefresh=Instance.new("TextButton",libModal)
btnRefresh.Size=UDim2.new(0,26,0,26); btnRefresh.Position=UDim2.new(1,-70,0,12)
btnRefresh.BackgroundColor3=THEME.purpleNeon; btnRefresh.BorderSizePixel=0
btnRefresh.Text="⟳"; btnRefresh.Font=Enum.Font.GothamBold; btnRefresh.TextSize=16
btnRefresh.TextColor3=THEME.text; btnRefresh.AutoButtonColor=false; btnRefresh.ZIndex=103
Instance.new("UICorner",btnRefresh).CornerRadius=UDim.new(1,0)

local btnCloseLib=Instance.new("TextButton",libModal)
btnCloseLib.Size=UDim2.new(0,26,0,26); btnCloseLib.Position=UDim2.new(1,-38,0,12)
btnCloseLib.BackgroundColor3=THEME.cardSoft; btnCloseLib.BorderSizePixel=0
btnCloseLib.Text="×"; btnCloseLib.Font=Enum.Font.GothamBold; btnCloseLib.TextSize=16
btnCloseLib.TextColor3=THEME.textDim; btnCloseLib.AutoButtonColor=false; btnCloseLib.ZIndex=103
Instance.new("UICorner",btnCloseLib).CornerRadius=UDim.new(1,0)

local listFrame=Instance.new("ScrollingFrame",libModal)
listFrame.Size=UDim2.new(1,-20,1,-80); listFrame.Position=UDim2.new(0,10,0,60)
listFrame.BackgroundTransparency=1; listFrame.BorderSizePixel=0
listFrame.ScrollBarThickness=4; listFrame.ScrollBarImageColor3=THEME.purple
listFrame.CanvasSize=UDim2.new(0,0,0,0); listFrame.AutomaticCanvasSize=Enum.AutomaticSize.Y; listFrame.ZIndex=102

local listLayout=Instance.new("UIListLayout",listFrame)
listLayout.Padding=UDim.new(0,8); listLayout.SortOrder=Enum.SortOrder.LayoutOrder

local emptyLabel=Instance.new("TextLabel",libModal)
emptyLabel.Size=UDim2.new(1,-20,0,100); emptyLabel.Position=UDim2.new(0,10,0,100)
emptyLabel.BackgroundTransparency=1
emptyLabel.Text="Sua biblioteca está vazia.\n\nGrave uma trajetória e clique em\n💾 Salvar para adicioná-la aqui."
emptyLabel.Font=Enum.Font.Gotham; emptyLabel.TextSize=12
emptyLabel.TextColor3=THEME.textMuted; emptyLabel.TextWrapped=true
emptyLabel.Visible=false; emptyLabel.ZIndex=103

-- COLAS TAF
local colasModal=Instance.new("Frame",gui)
colasModal.Size=UDim2.new(0,360,0,500); colasModal.Position=UDim2.new(0.5,-180,0.5,-250)
colasModal.BackgroundColor3=THEME.card; colasModal.BorderSizePixel=0
colasModal.Visible=false; colasModal.ZIndex=100
Instance.new("UICorner",colasModal).CornerRadius=UDim.new(0,20)
local colasStroke=Instance.new("UIStroke",colasModal)
colasStroke.Color=THEME.purple; colasStroke.Thickness=1.5; colasStroke.Transparency=0.3

local colasHeader=Instance.new("Frame",colasModal)
colasHeader.Size=UDim2.new(1,0,0,50); colasHeader.BackgroundColor3=THEME.purple
colasHeader.BorderSizePixel=0; colasHeader.ZIndex=101
Instance.new("UICorner",colasHeader).CornerRadius=UDim.new(0,20)

local colasTitle=Instance.new("TextLabel",colasModal)
colasTitle.Size=UDim2.new(1,-80,0,26); colasTitle.Position=UDim2.new(0,20,0,12)
colasTitle.BackgroundTransparency=1; colasTitle.Text="🗒  COLAS TAF"
colasTitle.Font=Enum.Font.GothamBlack; colasTitle.TextSize=16
colasTitle.TextColor3=THEME.text; colasTitle.TextXAlignment=Enum.TextXAlignment.Left; colasTitle.ZIndex=103

local btnCloseColas=Instance.new("TextButton",colasModal)
btnCloseColas.Size=UDim2.new(0,26,0,26); btnCloseColas.Position=UDim2.new(1,-38,0,12)
btnCloseColas.BackgroundColor3=THEME.cardSoft; btnCloseColas.BorderSizePixel=0
btnCloseColas.Text="×"; btnCloseColas.Font=Enum.Font.GothamBold; btnCloseColas.TextSize=16
btnCloseColas.TextColor3=THEME.textDim; btnCloseColas.AutoButtonColor=false; btnCloseColas.ZIndex=103
Instance.new("UICorner",btnCloseColas).CornerRadius=UDim.new(1,0)

local colasScroll=Instance.new("ScrollingFrame",colasModal)
colasScroll.Size=UDim2.new(1,-20,1,-70); colasScroll.Position=UDim2.new(0,10,0,60)
colasScroll.BackgroundTransparency=1; colasScroll.BorderSizePixel=0
colasScroll.ScrollBarThickness=5; colasScroll.ScrollBarImageColor3=THEME.purple
colasScroll.CanvasSize=UDim2.new(0,0,0,0); colasScroll.AutomaticCanvasSize=Enum.AutomaticSize.Y
colasScroll.ZIndex=102

local colasLayout=Instance.new("UIListLayout",colasScroll)
colasLayout.Padding=UDim.new(0,12); colasLayout.SortOrder=Enum.SortOrder.LayoutOrder

local function copyToClipboard(text)
    pcall(function()
        if setclipboard then setclipboard(text)
        elseif toclipboard then toclipboard(text)
        elseif writeclipboard then writeclipboard(text) end
    end)
end

for ci, cat in ipairs(COLAS) do
    local catHeader=Instance.new("TextLabel",colasScroll)
    catHeader.Size=UDim2.new(1,-10,0,36)
    catHeader.BackgroundColor3=cat.cor
    catHeader.BorderSizePixel=0
    catHeader.Text="   " .. cat.nome
    catHeader.Font=Enum.Font.GothamBlack
    catHeader.TextSize=14
    catHeader.TextColor3=Color3.fromRGB(255,255,255)
    catHeader.TextXAlignment=Enum.TextXAlignment.Left
    catHeader.LayoutOrder=ci*100
    catHeader.ZIndex=104
    Instance.new("UICorner",catHeader).CornerRadius=UDim.new(0,10)

    for ii, texto in ipairs(cat.itens) do
        local item=Instance.new("Frame",colasScroll)
        item.Size=UDim2.new(1,-10,0,50)
        item.BackgroundColor3=THEME.cardSoft
        item.BorderSizePixel=0
        item.LayoutOrder=ci*100 + ii
        item.ZIndex=103
        Instance.new("UICorner",item).CornerRadius=UDim.new(0,10)

        local sideBar=Instance.new("Frame",item)
        sideBar.Size=UDim2.new(0,3,1,-14); sideBar.Position=UDim2.new(0,6,0,7)
        sideBar.BackgroundColor3=cat.cor; sideBar.BorderSizePixel=0; sideBar.ZIndex=104
        Instance.new("UICorner",sideBar).CornerRadius=UDim.new(1,0)

        local lbl=Instance.new("TextLabel",item)
        lbl.Size=UDim2.new(1,-120,1,-8); lbl.Position=UDim2.new(0,16,0,4)
        lbl.BackgroundTransparency=1
        lbl.Text=texto; lbl.Font=Enum.Font.Gotham; lbl.TextSize=11
        lbl.TextColor3=THEME.text; lbl.TextXAlignment=Enum.TextXAlignment.Left
        lbl.TextWrapped=true; lbl.TextYAlignment=Enum.TextYAlignment.Center; lbl.ZIndex=104

        local btnCopy=Instance.new("TextButton",item)
        btnCopy.Size=UDim2.new(0,90,0,32); btnCopy.Position=UDim2.new(1,-100,0.5,-16)
        btnCopy.BackgroundColor3=THEME.green; btnCopy.BorderSizePixel=0
        btnCopy.Text="🗒 Copiar"; btnCopy.Font=Enum.Font.GothamBold
        btnCopy.TextSize=12; btnCopy.TextColor3=THEME.text
        btnCopy.AutoButtonColor=false; btnCopy.ZIndex=105
        Instance.new("UICorner",btnCopy).CornerRadius=UDim.new(0,8)

        local originalText=btnCopy.Text
        btnCopy.MouseButton1Click:Connect(function()
            copyToClipboard(texto)
            btnCopy.Text="✅ Copiado!"
            task.wait(1.2); btnCopy.Text=originalText
        end)
    end
end

-- PARKOURS
local parkModal=Instance.new("Frame",gui)
parkModal.Size=UDim2.new(0,360,0,500); parkModal.Position=UDim2.new(0.5,-180,0.5,-250)
parkModal.BackgroundColor3=THEME.card; parkModal.BorderSizePixel=0
parkModal.Visible=false; parkModal.ZIndex=100
Instance.new("UICorner",parkModal).CornerRadius=UDim.new(0,20)
local parkStroke=Instance.new("UIStroke",parkModal)
parkStroke.Color=THEME.purple; parkStroke.Thickness=1.5; parkStroke.Transparency=0.3

local parkHeader=Instance.new("Frame",parkModal)
parkHeader.Size=UDim2.new(1,0,0,50); parkHeader.BackgroundColor3=THEME.purple
parkHeader.BorderSizePixel=0; parkHeader.ZIndex=101
Instance.new("UICorner",parkHeader).CornerRadius=UDim.new(0,20)

local parkTitle=Instance.new("TextLabel",parkModal)
parkTitle.Size=UDim2.new(1,-80,0,26); parkTitle.Position=UDim2.new(0,20,0,12)
parkTitle.BackgroundTransparency=1; parkTitle.Text="🕵️  PARKOURS"
parkTitle.Font=Enum.Font.GothamBlack; parkTitle.TextSize=16
parkTitle.TextColor3=THEME.text; parkTitle.TextXAlignment=Enum.TextXAlignment.Left; parkTitle.ZIndex=103

local btnClosePark=Instance.new("TextButton",parkModal)
btnClosePark.Size=UDim2.new(0,26,0,26); btnClosePark.Position=UDim2.new(1,-38,0,12)
btnClosePark.BackgroundColor3=THEME.cardSoft; btnClosePark.BorderSizePixel=0
btnClosePark.Text="×"; btnClosePark.Font=Enum.Font.GothamBold; btnClosePark.TextSize=16
btnClosePark.TextColor3=THEME.textDim; btnClosePark.AutoButtonColor=false; btnClosePark.ZIndex=103
Instance.new("UICorner",btnClosePark).CornerRadius=UDim.new(1,0)

local parkScroll=Instance.new("ScrollingFrame",parkModal)
parkScroll.Size=UDim2.new(1,-20,1,-70); parkScroll.Position=UDim2.new(0,10,0,60)
parkScroll.BackgroundTransparency=1; parkScroll.BorderSizePixel=0
parkScroll.ScrollBarThickness=5; parkScroll.ScrollBarImageColor3=THEME.purple
parkScroll.CanvasSize=UDim2.new(0,0,0,0); parkScroll.AutomaticCanvasSize=Enum.AutomaticSize.Y
parkScroll.ZIndex=102

local parkLayout=Instance.new("UIListLayout",parkScroll)
parkLayout.Padding=UDim.new(0,10); parkLayout.SortOrder=Enum.SortOrder.LayoutOrder

local function renderParkours()
    for _, c in ipairs(parkScroll:GetChildren()) do
        if c:IsA("Frame") or c:IsA("TextLabel") then c:Destroy() end
    end

    if #PARKOURS_GLOBAIS == 0 then
        local aviso = Instance.new("TextLabel", parkScroll)
        aviso.Size = UDim2.new(1, -20, 0, 120)
        aviso.BackgroundTransparency = 1
        aviso.Text = "Nenhum parkour global ainda.\n\nOs parkours vão aparecer aqui\nquando forem adicionados."
        aviso.Font = Enum.Font.Gotham
        aviso.TextSize = 13
        aviso.TextColor3 = THEME.textMuted
        aviso.TextWrapped = true
        return
    end

    for i, pk in ipairs(PARKOURS_GLOBAIS) do
        local card = Instance.new("Frame", parkScroll)
        card.Size = UDim2.new(1, -10, 0, 80)
        card.BackgroundColor3 = THEME.cardSoft
        card.BorderSizePixel = 0
        card.LayoutOrder = i
        card.ZIndex = 103
        Instance.new("UICorner", card).CornerRadius = UDim.new(0, 12)

        local badge = Instance.new("TextLabel", card)
        badge.Size = UDim2.new(0, 60, 0, 16)
        badge.Position = UDim2.new(1, -70, 0, 8)
        badge.BackgroundColor3 = THEME.purpleNeon
        badge.BackgroundTransparency = 0.2
        badge.Text = "GLOBAL"
        badge.Font = Enum.Font.GothamBold
        badge.TextSize = 9
        badge.TextColor3 = THEME.text
        badge.ZIndex = 105
        Instance.new("UICorner", badge).CornerRadius = UDim.new(1, 0)

        local nameLbl = Instance.new("TextLabel", card)
        nameLbl.Size = UDim2.new(1, -160, 0, 20)
        nameLbl.Position = UDim2.new(0, 12, 0, 12)
        nameLbl.BackgroundTransparency = 1
        nameLbl.Text = pk.nome
        nameLbl.Font = Enum.Font.GothamBold
        nameLbl.TextSize = 13
        nameLbl.TextColor3 = THEME.text
        nameLbl.TextXAlignment = Enum.TextXAlignment.Left
        nameLbl.ZIndex = 104

        local infoLbl = Instance.new("TextLabel", card)
        infoLbl.Size = UDim2.new(1, -160, 0, 14)
        infoLbl.Position = UDim2.new(0, 12, 0, 32)
        infoLbl.BackgroundTransparency = 1
        infoLbl.Text = tostring(#pk.frames) .. " pontos"
        infoLbl.Font = Enum.Font.Gotham
        infoLbl.TextSize = 10
        infoLbl.TextColor3 = THEME.textMuted
        infoLbl.TextXAlignment = Enum.TextXAlignment.Left
        infoLbl.ZIndex = 104

        local btnRun = Instance.new("TextButton", card)
        btnRun.Size = UDim2.new(0, 90, 0, 32)
        btnRun.Position = UDim2.new(1, -100, 0.5, -16)
        btnRun.BackgroundColor3 = THEME.purple
        btnRun.BorderSizePixel = 0
        btnRun.Text = "▶ Rodar"
        btnRun.Font = Enum.Font.GothamBold
        btnRun.TextSize = 12
        btnRun.TextColor3 = THEME.text
        btnRun.AutoButtonColor = false
        btnRun.ZIndex = 105
        Instance.new("UICorner", btnRun).CornerRadius = UDim.new(0, 8)

        btnRun.MouseButton1Click:Connect(function()
            parkModal.Visible = false
            overlay.Visible = false
            task.delay(0.2, function()
                if playFrames then
                    playFrames(pk.frames, pk.nome, true)
                end
            end)
        end)
    end
end

-- ============ ANIMAÇÕES ============
local function getAnimator()
    if not S.humanoid and S.char then S.humanoid=S.char:FindFirstChildOfClass("Humanoid") end
    if not S.humanoid then return nil end
    local an=S.humanoid:FindFirstChildOfClass("Animator")
    if not an then an=Instance.new("Animator",S.humanoid) end
    return an
end

prepareAnimations = function()
    if not refreshChar() then return false end
    if not S.humanoid then S.humanoid=S.char:FindFirstChildOfClass("Humanoid") end
    local animator=getAnimator(); if not animator then return false end
    if S.animTracks then for _,t in pairs(S.animTracks) do pcall(function() t:Stop(0) end) end end
    S.animTracks={}
    local function load(id)
        if not id then return nil end
        local anim=Instance.new("Animation"); anim.AnimationId=id
        local ok,track=pcall(function() return animator:LoadAnimation(anim) end)
        if ok and track then track.Priority=Enum.AnimationPriority.Movement; track.Looped=true; return track end
        return nil
    end
    S.animTracks.idle=load(ANIM_IDLE)
    S.animTracks.walkFwd=load(ANIM_WALK_FWD)
    S.animTracks.jump=load(ANIM_JUMP)
    S.animTracks.fall=load(ANIM_FALL)
    S.animCurrent=nil
    return true
end

switchAnim = function(name)
    if not S.animTracks then return end
    if S.animCurrent==name then return end
    if S.animCurrent and S.animTracks[S.animCurrent] then
        pcall(function() S.animTracks[S.animCurrent]:Stop(0.15) end)
    end
    local t=S.animTracks[name]
    if t then pcall(function() t:Play(0.15,1,1) end); S.animCurrent=name end
end

local function updateAnimBySpeed(speed)
    if not S.animTracks then return end
    local hum=S.humanoid
    if hum and hum.FloorMaterial==Enum.Material.Air then
        if hum:GetState()==Enum.HumanoidStateType.Freefall then
            switchAnim(speed>5 and "fall" or "jump")
        end
        return
    end
    if speed<0.5 then switchAnim("idle") return end
    switchAnim("walkFwd")
end

local function playWalkAnim()
    if not prepareAnimations() then return end
    switchAnim("idle")
end

stopWalkAnim = function()
    if S.animTracks then for _,t in pairs(S.animTracks) do pcall(function() t:Stop(0.15) end) end end
    S.animTracks=nil; S.animCurrent=nil
end

-- PLAYBACK
stopPlayback = function(silent)
    S.playing=false
    if S.playConn then S.playConn:Disconnect() S.playConn=nil end
    stopWalkAnim()
    disableShiftLock()
    setStatus("ready")
    btnPlay.BackgroundColor3=colorPlay
    if not silent then notify("Reprodução interrompida") end
end

playFrames = function(frames,displayName,drawLineToo)
    if not frames or #frames<2 then notify("Trajetória inválida") return end
    if not refreshChar() then notify("Personagem não encontrado") return end
    if S.playing then stopPlayback(true) end
    if S.recording then
        S.recording=false
        if S.recordConn then S.recordConn:Disconnect() S.recordConn=nil end
        txtRecord.Text="Gravar"; btnRecord.BackgroundColor3=colorRecord
    end
    local cfs,times={},{}
    for i,f in ipairs(frames) do
        local cf
        if f.cf then cf=f.cf
        elseif f.p then cf=CFrame.new(f.p[1],f.p[2],f.p[3]) end
        if cf then cfs[#cfs+1]=cf; times[#times+1]=f.t or ((i-1)/RECORD_HZ) end
    end
    if #cfs<2 then notify("Trajetória inválida") return end
    if drawLineToo then drawLine(cfs) end
    libModal.Visible=false; colasModal.Visible=false; parkModal.Visible=false; overlay.Visible=false
    overlay.BackgroundTransparency = 1

    S.playing=true; S.playCFs=cfs; S.playTimes=times
    S.playIndex=1; S.playStartTime=os.clock(); S.playTotal=times[#times]
    S.lastAnimSpeed=0; S.lastPlayCF=nil
    S.smoothLook = nil

    setStatus("playing")
    TweenService:Create(btnPlay,TweenInfo.new(0.3),{BackgroundColor3=THEME.green}):Play()
    notify("Reproduzindo: "..(displayName or "trajetória"))
    playWalkAnim()
    enableShiftLock()

    local animUpdateCounter = 0

    S.playConn=RunService.RenderStepped:Connect(function(dt)
        if not S.playing then return end
        local hrp=S.hrp
        if not hrp or not hrp.Parent then stopPlayback(true) return end
        local elapsed=os.clock()-S.playStartTime
        if elapsed>=S.playTotal then
            pcall(function() hrp.CFrame=S.playCFs[#S.playCFs] end)
            stopPlayback(true); notify("Reprodução concluída"); return
        end

        local idx=S.playIndex
        while idx<#S.playTimes and S.playTimes[idx+1]<=elapsed do idx=idx+1 end
        S.playIndex=idx
        local i1,i2=idx,math.min(idx+1,#S.playTimes)
        if i1==i2 then
            pcall(function() hrp.CFrame=S.playCFs[i1] end)
            return
        end
        local t1,t2=S.playTimes[i1],S.playTimes[i2]
        local alpha=(t2>t1) and ((elapsed-t1)/(t2-t1)) or 0
        alpha=math.clamp(alpha,0,1)

        local pos1 = S.playCFs[i1].Position
        local pos2 = S.playCFs[i2].Position
        local interpPos = pos1:Lerp(pos2, alpha)

        local lookAheadIdx = math.min(i2 + 3, #S.playCFs)
        local targetPos = S.playCFs[lookAheadIdx].Position
        local moveDir = targetPos - interpPos
        moveDir = Vector3.new(moveDir.X, 0, moveDir.Z)

        if moveDir.Magnitude > 0.05 then
            moveDir = moveDir.Unit
            if not S.smoothLook then
                S.smoothLook = moveDir
            else
                S.smoothLook = S.smoothLook:Lerp(moveDir, 0.25)
                if S.smoothLook.Magnitude > 0.01 then
                    S.smoothLook = S.smoothLook.Unit
                end
            end
            local finalCF = CFrame.lookAt(interpPos, interpPos + S.smoothLook)
            pcall(function() hrp.CFrame = finalCF end)
        else
            local currentLook = hrp.CFrame.LookVector
            local flatLook = Vector3.new(currentLook.X, 0, currentLook.Z)
            if flatLook.Magnitude > 0.01 then
                flatLook = flatLook.Unit
                local finalCF = CFrame.lookAt(interpPos, interpPos + flatLook)
                pcall(function() hrp.CFrame = finalCF end)
            else
                pcall(function() hrp.CFrame = CFrame.new(interpPos) end)
            end
        end

        animUpdateCounter = animUpdateCounter + 1
        if animUpdateCounter >= 3 then
            animUpdateCounter = 0
            local lastCF=S.lastPlayCF or hrp.CFrame
            local dist=(interpPos - lastCF.Position).Magnitude
            local speed=dt>0 and (dist/(dt*3)) or 0
            if speed>200 then speed=S.lastAnimSpeed end
            S.lastAnimSpeed=speed
            updateAnimBySpeed(speed)
        end
        S.lastPlayCF = hrp.CFrame
    end)
end

-- RECORD
local function startRecording()
    if not refreshChar() then notify("Personagem não encontrado") return end
    S.frames=table.create(MAX_FRAMES)
    S.recording=true
    S.recordStart=os.clock()
    createTrail(); clearLine(); updateInfo()
    txtRecord.Text="Parar"; iconRecord.Text="⏹"
    TweenService:Create(btnRecord,TweenInfo.new(0.25),{BackgroundColor3=THEME.red}):Play()
    setStatus("recording"); notify("Gravação 120 FPS iniciada")
    enableShiftLock()

    local lastPos,lastDir
    local accumulated = 0

    S.recordConn = RunService.Heartbeat:Connect(function(dt)
        if not S.recording then return end
        accumulated = accumulated + dt
        if accumulated < RECORD_INTERVAL then return end
        accumulated = 0

        local hrp = S.hrp
        if hrp and hrp.Parent then
            local cf = hrp.CFrame
            local pos, dir = cf.Position, cf.LookVector
            local save = false
            if not lastPos then save = true
            else
                if (pos-lastPos).Magnitude > RECORD_MIN_DIST or (dir-lastDir).Magnitude > 0.05 then
                    save = true
                end
            end
            if save and #S.frames < MAX_FRAMES then
                S.frames[#S.frames+1] = {cf=cf, t=os.clock()-S.recordStart}
                lastPos, lastDir = pos, dir
                updateInfo()
            end
        end
    end)
end

local function stopRecording()
    S.recording=false
    if S.recordConn then S.recordConn:Disconnect() S.recordConn=nil end
    txtRecord.Text="Gravar"; iconRecord.Text="⏺"
    TweenService:Create(btnRecord,TweenInfo.new(0.25),{BackgroundColor3=colorRecord}):Play()
    setStatus("ready"); updateInfo()
    disableShiftLock()
    if #S.frames>1 then
        local cfs=table.create(#S.frames)
        for i,f in ipairs(S.frames) do cfs[i]=f.cf end
        drawLine(cfs); notify(string.format("Gravado! %d pontos",#S.frames))
    else notify("Nada foi gravado") end
end

local function doSave(name)
    if #S.frames<2 then notify("Nada para salvar") return end
    if name=="" then name="trajetoria_"..os.date("%Y%m%d_%H%M%S") end
    local data={name=name,savedAt=os.time(),frames={}}
    for i,f in ipairs(S.frames) do
        data.frames[i]={p={f.cf.Position.X,f.cf.Position.Y,f.cf.Position.Z},t=f.t}
    end
    S.saves[name]={name=name,frames=S.frames,source=SAVE_FOLDER.."/"..sanitize(name)..".json"}
    local ok=saveToDisk(name,data)
    notify((ok and "Salvo: " or "Salvo em memória: ")..name)
end

local function refreshLibrary()
    for _,c in ipairs(listFrame:GetChildren()) do
        if c:IsA("Frame") or c:IsA("TextButton") then c:Destroy() end
    end
    local names={}
    for n in pairs(S.saves) do names[#names+1]=n end
    table.sort(names,function(a,b) return a:lower()<b:lower() end)
    libCount.Text=#names.." itens"
    if #names==0 then emptyLabel.Visible=true return end
    emptyLabel.Visible=false
    for idx,name in ipairs(names) do
        local data=S.saves[name]; local count=data.frames and #data.frames or 0
        local item=Instance.new("Frame",listFrame)
        item.Size=UDim2.new(1,-8,0,56); item.BackgroundColor3=THEME.cardSoft
        item.BorderSizePixel=0; item.LayoutOrder=idx; item.ZIndex=103
        Instance.new("UICorner",item).CornerRadius=UDim.new(0,12)
        local playBtn=Instance.new("TextButton",item)
        playBtn.Size=UDim2.new(1,-50,1,0); playBtn.BackgroundTransparency=1
        playBtn.Text=""; playBtn.ZIndex=104
        local nameLbl=Instance.new("TextLabel",item)
        nameLbl.Size=UDim2.new(1,-120,0,18); nameLbl.Position=UDim2.new(0,54,0,8)
        nameLbl.BackgroundTransparency=1; nameLbl.Text=name
        nameLbl.Font=Enum.Font.GothamBold; nameLbl.TextSize=12
        nameLbl.TextColor3=THEME.text; nameLbl.TextXAlignment=Enum.TextXAlignment.Left
        nameLbl.TextTruncate=Enum.TextTruncate.AtEnd; nameLbl.ZIndex=104
        local tagLbl=Instance.new("TextLabel",item)
        tagLbl.Size=UDim2.new(1,-120,0,14); tagLbl.Position=UDim2.new(0,54,0,26)
        tagLbl.BackgroundTransparency=1; tagLbl.Text=count.." pontos"
        tagLbl.Font=Enum.Font.Gotham; tagLbl.TextSize=10
        tagLbl.TextColor3=THEME.textMuted; tagLbl.TextXAlignment=Enum.TextXAlignment.Left; tagLbl.ZIndex=104
        local iconBox=Instance.new("Frame",item)
        iconBox.Size=UDim2.new(0,32,0,32); iconBox.Position=UDim2.new(0,12,0.5,-16)
        iconBox.BackgroundColor3=THEME.purple; iconBox.BorderSizePixel=0; iconBox.ZIndex=104
        Instance.new("UICorner",iconBox).CornerRadius=UDim.new(0,8)
        local iconT=Instance.new("TextLabel",iconBox)
        iconT.Size=UDim2.new(1,0,1,0); iconT.BackgroundTransparency=1
        iconT.Text="▶"; iconT.Font=Enum.Font.GothamBold; iconT.TextSize=13
        iconT.TextColor3=THEME.text; iconT.ZIndex=105
        local delBtn=Instance.new("TextButton",item)
        delBtn.Size=UDim2.new(0,34,0,34); delBtn.Position=UDim2.new(1,-42,0.5,-17)
        delBtn.BackgroundColor3=THEME.card; delBtn.BorderSizePixel=0
        delBtn.Text="🗑"; delBtn.Font=Enum.Font.GothamBold; delBtn.TextSize=14
        delBtn.TextColor3=THEME.textMuted; delBtn.AutoButtonColor=false; delBtn.ZIndex=105
        Instance.new("UICorner",delBtn).CornerRadius=UDim.new(0,8)
        playBtn.MouseButton1Click:Connect(function() playFrames(data.frames,name,true) end)
        delBtn.MouseButton1Click:Connect(function()
            S.saves[name]=nil; deleteFromDisk(name); notify("Removido: "..name); refreshLibrary()
        end)
    end
end

local function loadAllSaves()
    S.saves={}
    if not hasFS then return 0 end
    local ok,files=pcall(listfiles,SAVE_FOLDER)
    if not ok or type(files)~="table" then return 0 end
    for _,path in ipairs(files) do
        if path:lower():match("%.json$") then
            local baseName=path:match("([^/\\]+)%.json$") or "trajetoria"
            local okR,content=pcall(readfile,path)
            if okR and content then
                local data=nil
                pcall(function() data=HttpService:JSONDecode(content) end)
                if data and data.frames and #data.frames>=2 then
                    local frames={}
                    for i,f in ipairs(data.frames) do
                        if f.p then frames[i]={cf=CFrame.new(f.p[1],f.p[2],f.p[3]),t=f.t or ((i-1)/30)} end
                    end
                    S.saves[baseName]={name=baseName,frames=frames,source=path}
                end
            end
        end
    end
    local total=0; for _ in pairs(S.saves) do total=total+1 end
    return total
end

-- EVENTOS
btnCancelModal.MouseButton1Click:Connect(closeModal)
overlay.MouseButton1Click:Connect(function()
    closeModal(); libModal.Visible=false; colasModal.Visible=false; parkModal.Visible=false
    overlay.Visible=false; overlay.BackgroundTransparency=1
end)
btnConfirmModal.MouseButton1Click:Connect(function()
    local n=inputBox.Text:gsub("^%s+",""):gsub("%s+$","")
    closeModal(); task.wait(0.1); doSave(n)
end)
inputBox.FocusLost:Connect(function(enterPressed)
    if enterPressed then
        local n=inputBox.Text:gsub("^%s+",""):gsub("%s+$","")
        closeModal(); task.wait(0.1); doSave(n)
    end
end)

btnRefresh.MouseButton1Click:Connect(function()
    local n=loadAllSaves(); refreshLibrary(); notify("Atualizado • "..n.." itens")
end)

local function openLibrary()
    loadAllSaves(); refreshLibrary()
    libModal.Visible=true; overlay.Visible=true; overlay.BackgroundTransparency=0.5
end
local function closeLibrary()
    libModal.Visible=false; overlay.Visible=false; overlay.BackgroundTransparency=1
end
btnCloseLib.MouseButton1Click:Connect(closeLibrary)

local function openColas()
    colasModal.Visible=true; overlay.Visible=true; overlay.BackgroundTransparency=0.5
end
local function closeColas()
    colasModal.Visible=false; overlay.Visible=false; overlay.BackgroundTransparency=1
end
btnCloseColas.MouseButton1Click:Connect(closeColas)

local function openPark()
    renderParkours()
    parkModal.Visible=true; overlay.Visible=true; overlay.BackgroundTransparency=0.5
end
local function closePark()
    parkModal.Visible=false; overlay.Visible=false; overlay.BackgroundTransparency=1
end
btnClosePark.MouseButton1Click:Connect(closePark)

btnRecord.MouseButton1Click:Connect(function()
    if S.recording then stopRecording()
    else if S.playing then stopPlayback(true) end; startRecording() end
end)
btnPlay.MouseButton1Click:Connect(function()
    if S.playing then return end
    if #S.frames<2 then notify("Nada para reproduzir") return end
    playFrames(S.frames,"gravação atual",false)
end)
btnStopPlay.MouseButton1Click:Connect(function()
    if S.playing then stopPlayback(false) else notify("Nada está sendo reproduzido") end
end)
btnSave.MouseButton1Click:Connect(function()
    if #S.frames<2 then notify("Nada para salvar. Grave primeiro!") return end
    openModal()
end)
btnClear.MouseButton1Click:Connect(function()
    if S.recording then stopRecording() end
    if S.playing then stopPlayback(true) end
    S.frames=table.create(MAX_FRAMES); clearLine(); updateInfo(); setStatus("ready")
    notify("Gravação apagada")
end)
btnLibrary.MouseButton1Click:Connect(openLibrary)
btnColas.MouseButton1Click:Connect(openColas)
btnParkour.MouseButton1Click:Connect(openPark)

btnClose.MouseButton1Click:Connect(function() minimize() end)

LocalPlayer.CharacterAdded:Connect(function()
    task.wait(1)
    destroyTrail(); stopWalkAnim(); disableShiftLock()
    if S.recording then
        S.recording=false
        if S.recordConn then S.recordConn:Disconnect() S.recordConn=nil end
        setStatus("ready")
        txtRecord.Text="Gravar"; iconRecord.Text="⏺"; btnRecord.BackgroundColor3=colorRecord
    end
    if S.playing then stopPlayback(true) end
end)

-- ============ INIT ============
pcall(ensureFolder)
local total = 0
pcall(function() total = loadAllSaves() end)

setStatus("ready")
updateInfo()
notify("PRIME HUB v1.4 • "..tostring(total).." trajetórias • ShiftLock ON")
print("[PRIME HUB] Script carregado com sucesso!")
