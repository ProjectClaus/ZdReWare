-- New example script written by wally
-- You can suggest changes with a pull request or something

print('ZdReWare is LOADED!')

local Camera = game:GetService("Workspace").CurrentCamera
local CharcaterMiddle = game:GetService("Workspace").Ignore.LocalCharacter.Middle

local repo = 'https://raw.githubusercontent.com/wally-rblx/LinoriaLib/main/'

local Library = loadstring(game:HttpGet(repo .. 'Library.lua'))()
local ThemeManager = loadstring(game:HttpGet(repo .. 'addons/ThemeManager.lua'))()
local SaveManager = loadstring(game:HttpGet(repo .. 'addons/SaveManager.lua'))()

local Window = Library:CreateWindow({
    -- Set Center to true if you want the menu to appear in the center
    -- Set AutoShow to true if you want the menu to appear when it is created
    -- Position and Size are also valid options here
    -- but you do not need to define them unless you are changing them :)

    Title = 'ZdReWare - Test Version',
    Center = true, 
    AutoShow = true,
})

-- You do not have to set your tabs & groups up this way, just a prefrence.
local Tabs = {
    -- Creates a new tab titled Main
    Main = Window:AddTab('Main'), 
    ['Dev'] = Window:AddTab("Misc"), 
    ['UI Settings'] = Window:AddTab('UI Settings'),
}

-- Groupbox and Tabbox inherit the same functions
-- except Tabboxes you have to call the functions on a tab (Tabbox:AddTab(name))
local DevLeftBox = Tabs.Dev:AddLeftGroupbox('Misc Page 1')
local DevRightBox = Tabs.Dev:AddRightGroupbox('Misc Page 2')
local DevLeftBox2 = Tabs.Dev:AddLeftGroupbox('Misc Page 3')
local DevRightBox2 = Tabs.Dev:AddRightGroupbox('Misc Page 4')
local MainLeftBox = Tabs.Main:AddLeftGroupbox('Blatant Page 1')
local MainRightBox = Tabs.Main:AddRightGroupbox('Blatant Page 2')

local MyButton = DevLeftBox:AddButton('FOV', function()
    local varsglobal = {
        visuals = {
            FovChanger = true, 
            FovAdd = 25,
            OldFov = workspace.CurrentCamera.FieldOfView,
            ZoomAmt = 0,
            FovZoom = false,
        },
    }
    
    local function updateFOV()
        if varsglobal.visuals.FovChanger then
            workspace.CurrentCamera.FieldOfView = varsglobal.visuals.OldFov + varsglobal.visuals.FovAdd
        else
            workspace.CurrentCamera.FieldOfView = varsglobal.visuals.OldFov
        end
    end
    game:GetService("RunService").RenderStepped:Connect(updateFOV)
end)

-- Button:AddButton
-- Arguments: Text, Callback
-- Adds a sub button to the side of the main button

local MyButton2 = MyButton:AddButton('Quick Crouch', function()
Workspace.Ignore.LocalCharacter.Top.CrouchForce.Stiffness = 100000000000000000000000000000000000000000000000000
end)

local MyButton3 = DevRightBox:AddButton('Fullbright', function()
local Light = game:GetService("Lighting")

function dofullbright()
Light.Ambient = Color3.new(1, 1, 1)
Light.ColorShift_Bottom = Color3.new(1, 1, 1)
Light.ColorShift_Top = Color3.new(1, 1, 1)
end

dofullbright()

Light.LightingChanged:Connect(dofullbright)
end)

local MyButton4 = MyButton3:AddButton('No Recoil', function()
end)

local MyButton5 = MainLeftBox:AddButton('Hitbox (RAGE)', function()
    local antihitbox
    antihitbox = hookmetamethod(game, "__newindex", newcclosure(function(...)
    local self, k = ...
    if not checkcaller() and k == "Size" and self.Name == "Head" then
      return Vector3.new(1.672248125076294, 0.835624098777771, 0.835624098777771)
    end
    return antihitbox(...)
    end))
    
    local HitboxExpander = {HitBX = 8.25,HitBY = 8.25,HitBZ = 8.25,}
    for v, i in pairs(workspace:GetChildren()) do
      if i:FindFirstChild("HumanoidRootPart") then
        i.Head.Size = Vector3.new(HitboxExpander.HitBX, HitboxExpander.HitBY, HitboxExpander.HitBZ)
        i.Head.CanCollide = false
        i.Head.Color = Color3.fromRGB(255, 0, 10)
        i.Head.Material = "ForceField"
      end
    end
    game.ReplicatedStorage.Player.Head.Size = Vector3.new(HitboxExpander.HitBX, HitboxExpander.HitBY, HitboxExpander.HitBZ)
end)

local MyButton6 = MainRightBox:AddButton('ESP (Working for all(s))', function()
--Tables
local Functions = {}
local Esp = {Settings={
    Boxes=true,BoxesOutline=true,BoxesColor=Color3.fromRGB(255,255,255),BoxesOutlineColor=Color3.fromRGB(0,0,0),
    Sleeping=true,SleepingColor=Color3.fromRGB(255,255,255),
    Distances=true,DistanceColor=Color3.fromRGB(255,255,255),
    Armour=true,ArmourColor=Color3.fromRGB(255,255,255),
    Tool=true,ToolColor=Color3.fromRGB(255,255,255),
    ViewAngle=true,ViewAngleColor=Color3.fromRGB(255,255,255),ViewAngleThickness=1,ViewAngleTransparrency=1,
    TextFont=2,TextOutline=true,TextSize=15,RenderDistance=1500,TeamCheck=false,TargetSleepers=false,MinTextSize=8
},Drawings={},Connections={},Players={}}
local Fonts = {["UI"]=0,["System"]=1,["Plex"]=2,["Monospace"]=3}
local cache = {}

--Locals
local Camera = game:GetService("Workspace").CurrentCamera
local CharcaterMiddle = game:GetService("Workspace").Ignore.LocalCharacter.Middle

--Functions
function Functions:IsSleeping(Model)
    if Model and Model:FindFirstChild("AnimationController") and Model.AnimationController:FindFirstChild("Animator") then
		for i,v in pairs(Model.AnimationController.Animator:GetPlayingAnimationTracks()) do
            if v.Animation.AnimationId == "rbxassetid://13280887764" then
                return true
            else
                return false
            end
        end
    end
end
function Functions:Draw(Type,Propities)
    if not Type and not Propities then return end
    local drawing = Drawing.new(Type)
    for i,v in pairs(Propities) do
        drawing[i] = v
    end
    table.insert(Esp.Drawings,drawing)
    return drawing
end
function Esp:CreateEsp(PlayerTable)
    if not PlayerTable then return end
    local drawings = {}
    drawings.BoxOutline = Functions:Draw("Square",{Thickness=2,Filled=false,Transparency=1,Color=Esp.Settings.BoxesOutlineColor,Visible=false,ZIndex = -1,Visible=false});
    drawings.Box = Functions:Draw("Square",{Thickness=1,Filled=false,Transparency=1,Color=Esp.Settings.BoxesColor,Visible=false,ZIndex = 2,Visible=false});
    drawings.Sleeping = Functions:Draw("Text",{Text = "Nil",Font=Esp.Settings.TextFont,Size=Esp.Settings.TextSize,Center=true,Outline=Esp.Settings.TextOutline,Color = Esp.Settings.SleepingColor,ZIndex = 2,Visible=false})
    drawings.Distance = Functions:Draw("Text",{Text = "Nil",Font=Esp.Settings.TextFont,Size=Esp.Settings.TextSize,Center=true,Outline=Esp.Settings.TextOutline,Color = Esp.Settings.SleepingColor,ZIndex = 2,Visible=false})
    drawings.Armour = Functions:Draw("Text",{Text = "Naked",Font=Esp.Settings.TextFont,Size=Esp.Settings.TextSize,Center=false,Outline=Esp.Settings.TextOutline,Color = Esp.Settings.ArmourColor,ZIndex = 2,Visible=false})
    drawings.ViewAngle = Functions:Draw("Line",{Thickness=Esp.Settings.ViewAngleThickness,Transparency=Esp.Settings.ViewAngleTransparrency,Color=Esp.Settings.ViewAngleColor,ZIndex=2,Visible=false})
    drawings.PlayerTable = PlayerTable
    Esp.Players[PlayerTable.model] = drawings
end
function Esp:RemoveEsp(PlayerTable)
    if not PlayerTable and PlayerTable.model ~= nil then return end
    esp = Esp.Players[PlayerTable.model];
    if not esp then return end
    for i, v in pairs(esp) do
        if not type(v) == "table" then
            v:Remove();
        end
    end
    Esp.Players[PlayerTable.model] = nil;
end

function Esp:UpdateEsp()
    for i,v in pairs(Esp.Players) do
        local Character = i
        local Position,OnScreen = Camera:WorldToViewportPoint(Character:GetPivot().Position);
        local scale = 1 / (Position.Z * math.tan(math.rad(Camera.FieldOfView * 0.5)) * 2) * 100;
        local w,h = math.floor(40 * scale), math.floor(55 * scale);
        local x,y = math.floor(Position.X), math.floor(Position.Y);
        local Distance = (CharcaterMiddle:GetPivot().Position-Character:GetPivot().Position).Magnitude
        local BoxPosX,BoxPosY = math.floor(x - w * 0.5),math.floor(y - h * 0.5)
        local offsetCFrame = CFrame.new(0, 0, -4)
        local sleeping = Functions:IsSleeping(Character)
        if Character and Character:FindFirstChild("HumanoidRootPart") and Character:FindFirstChild("Head") then
            local TeamTag = Character.Head.Teamtag.Enabled
            if OnScreen == true and Esp.Settings.Boxes == true and Distance <= Esp.Settings.RenderDistance then
                if Esp.Settings.TeamCheck == true and TeamTag == false then 
                    v.BoxOutline.Visible = Esp.Settings.BoxesOutline;v.Box.Visible = true
                elseif Esp.Settings.TeamCheck == true and TeamTag == true then
                    v.BoxOutline.Visible = false;v.Box.Visible = false
                else
                    v.BoxOutline.Visible = Esp.Settings.BoxesOutline;v.Box.Visible = true
                end
                if Esp.Settings.TargetSleepers == true and sleeping == true then
                    v.BoxOutline.Visible = false;v.Box.Visible = false
                end
                v.BoxOutline.Position = Vector2.new(BoxPosX,BoxPosY);v.BoxOutline.Size = Vector2.new(w,h)
                v.Box.Position = Vector2.new(BoxPosX,BoxPosY);v.Box.Size = Vector2.new(w,h)
                v.Box.Color = Esp.Settings.BoxesColor;v.BoxOutline.Color = Esp.Settings.BoxesOutlineColor
            else
                v.BoxOutline.Visible = false;v.Box.Visible = false
            end
            if OnScreen == true and Esp.Settings.Sleeping == true and Distance <= Esp.Settings.RenderDistance then
                if sleeping == true then v.Sleeping.Text = "Sleeping" else v.Sleeping.Text = "Awake" end
                if Esp.Settings.TeamCheck == true and TeamTag == false then
                    v.Sleeping.Visible = true
                elseif Esp.Settings.TeamCheck == true and TeamTag == true then
                    v.Sleeping.Visible = false
                end
                if Esp.Settings.TargetSleepers == true and sleeping == true then v.Sleeping.Visible = false end

                v.Sleeping.Outline=Esp.Settings.TextOutline;v.Sleeping.Color=Esp.Settings.SleepingColor;v.Sleeping.Size=math.max(math.min(math.abs(Esp.Settings.TextSize*scale),Esp.Settings.TextSize),Esp.Settings.MinTextSize);v.Sleeping.Color = Esp.Settings.SleepingColor;v.Sleeping.Font=Esp.Settings.TextFont;v.Sleeping.Position = Vector2.new(x,math.floor(y-h*0.5-v.Sleeping.TextBounds.Y))
            else
                v.Sleeping.Visible=false
            end
            if OnScreen == true and Esp.Settings.Distances == true and Distance <= Esp.Settings.RenderDistance then
                if Esp.Settings.TeamCheck == true and TeamTag == false then
                    v.Distance.Visible = true
                elseif Esp.Settings.TeamCheck == true and TeamTag == true then
                    v.Distance.Visible = false
                end
                if Esp.Settings.TargetSleepers == true and sleeping == true then v.Distance.Visible = false end

                if Esp.Settings.Sleeping == false then
                    v.Distance.Text = math.floor(Distance).."s"
                else
                    v.Distance.Text = math.floor(Distance).."s"
                end
                v.Distance.Outline=Esp.Settings.TextOutline;v.Distance.Color=Esp.Settings.SleepingColor;v.Distance.Size=math.max(math.min(math.abs(Esp.Settings.TextSize*scale),Esp.Settings.TextSize),Esp.Settings.MinTextSize);v.Distance.Color = Esp.Settings.DistanceColor;v.Distance.Font=Esp.Settings.TextFont;v.Distance.Position = Vector2.new(x,math.floor(y+h*.5))
            else
                v.Distance.Visible = false
            end
            if OnScreen == true and Esp.Settings.Armour == true and Distance <= Esp.Settings.RenderDistance then
                if Character.Armor:FindFirstChildOfClass("Folder") then v.Armour.Text = "Armoured" else v.Armour.Text = "Naked" end
                if Esp.Settings.TeamCheck == true and TeamTag == false then v.Armour.Visible = true elseif Esp.Settings.TeamCheck == true and TeamTag == true then v.Armour.Visible = false else v.Armour.Visible = true end
                if Esp.Settings.TargetSleepers == true and sleeping == true then v.Armour.Visible = false end
                v.Armour.Outline=Esp.Settings.TextOutline;v.Armour.Size = math.max(math.min(math.abs(Esp.Settings.TextSize*scale),Esp.Settings.TextSize),Esp.Settings.MinTextSize);
                v.Armour.Position=Vector2.new(math.floor((BoxPosX+w)+v.Armour.TextBounds.X/10),BoxPosY+v.Armour.TextBounds.Y*1.55*0.5-((v.Armour.TextBounds.Y*2)*0.5));
                v.Armour.Color = Esp.Settings.ArmourColor;v.Armour.Font=Esp.Settings.TextFont
            else
                v.Armour.Visible = false
            end
            if OnScreen == true and Esp.Settings.ViewAngle == true and Distance <= Esp.Settings.RenderDistance then
                if Esp.Settings.TeamCheck == true and TeamTag == false then v.ViewAngle.Visible = true elseif Esp.Settings.TeamCheck == true and TeamTag == true then v.ViewAngle.Visible = false else v.ViewAngle.Visible = true end
                if Esp.Settings.TargetSleepers == true and sleeping == true then v.ViewAngle.Visible = false end
                v.ViewAngle.Color = Esp.Settings.ViewAngleColor;v.ViewAngle.Thickness=Esp.Settings.ViewAngleThickness;v.Transparency=Esp.Settings.ViewAngleTransparrency;
                local headpos = Camera:WorldToViewportPoint(Character.Head.Position)
                local offsetCFrame = CFrame.new(0, 0, -4)
                v.ViewAngle.From = Vector2.new(headpos.X, headpos.Y)
                local value = math.clamp(1/Distance*100, 0.1, 1)
                local dir = Character.Head.CFrame:ToWorldSpace(offsetCFrame)
                offsetCFrame = offsetCFrame * CFrame.new(0, 0, 0.4)
                local dirpos = Camera:WorldToViewportPoint(Vector3.new(dir.X, dir.Y, dir.Z))
                if OnScreen == true then
                    v.ViewAngle.To = Vector2.new(dirpos.X, dirpos.Y)
                    offsetCFrame = CFrame.new(0, 0, -4)
                end
            else
                v.ViewAngle.Visible = false
            end
        else
            v.Box.Visible=false;v.BoxOutline.Visible=false;v.Armour.Visible=false;v.Distance.Visible=false;v.ViewAngle.Visible=false;v.Sleeping.Visible=false;
        end
    end
end

--Connections
local PlayerUpdater = game:GetService("RunService").RenderStepped
local PlayerConnection = PlayerUpdater:Connect(function()
    Esp:UpdateEsp()
end)

--Init Functions
for i,v in pairs(workspace:GetChildren()) do
	if v:FindFirstChild("HumanoidRootPart") then
        table.insert(cache,v)
        Esp:CreateEsp({model=v})
	end
end

game:GetService("Workspace").ChildAdded:Connect(function(child)
    if child:FindFirstChild("HumanoidRootPart") and not table.find(cache,child) then
        table.insert(cache,child)
        Esp:CreateEsp({model=child})
    end
end)

local PlayerVisualTabbox = Tabs.Visual:AddLeftTabbox()
local PlayerVisualTab = PlayerVisualTabbox:AddTab('Players')
local PlayerSettingsVisualTab = PlayerVisualTabbox:AddTab('Settings')

PlayerVisualTab:AddToggle('Boxes',{Text='Boxes',Default=false}):AddColorPicker('BoxesColor',{Default=Color3.fromRGB(0,255,239),Title='Color'}):AddColorPicker('BoxesOutlineColor',{Default=Color3.fromRGB(0,0,0),Title='Color'})
PlayerVisualTab:AddToggle('Distances',{Text='Distance',Default=false}):AddColorPicker('DistancesColor',{Default=Color3.fromRGB(0,255,239),Title='Color'})
PlayerVisualTab:AddToggle('Sleeping',{Text='Sleeping',Default=false}):AddColorPicker('SleepingColor',{Default=Color3.fromRGB(0,255,239),Title='Color'})
PlayerVisualTab:AddToggle('Armour',{Text='Armour',Default=false}):AddColorPicker('ArmourColor',{Default=Color3.fromRGB(0,255,239),Title='Color'})
PlayerVisualTab:AddToggle('ViewAngle',{Text='View Angle',Default=false}):AddColorPicker('ViewAngleColor',{Default=Color3.fromRGB(0,255,239),Title='Color'})
end)

local MyButton7 = DevRightBox2:AddButton('Crosshair', function()
local CrossHairX = Drawing.new("Circle")

CrossHairX.Position = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
CrossHairX.Thickness = 1
CrossHairX.ZIndex = 3
CrossHairX.Visible = true
CrossHairX.Filled = true
CrossHairX.Radius = 3
CrossHairX.Color = Color3.fromRGB(0, 0, 0)
end)

DevLeftBox2:AddDivider()

DevLeftBox2:AddSlider('MySlider', {
    Text = 'Gravity',

    Default = 75,
    Min = 65,
    Max = 75,
    Rounding = 1,

    Compact = false, -- If set to true, then it will hide the label
})

-- Options is a table added to getgenv() by the library
-- You index Options with the specified index, in this case it is 'MySlider'
-- To get the value of the slider you do slider.Value

local Number = Options.MySlider.Value
Options.MySlider:OnChanged(function(value)
Workspace.Gravity = value
end)

MainLeftBox:AddDivider()

local MyButton8 = MainLeftBox:AddButton('Hitbox (LEGIT)', function()
    local antihitbox
    antihitbox = hookmetamethod(game, "__newindex", newcclosure(function(...)
    local self, k = ...
    if not checkcaller() and k == "Size" and self.Name == "Head" then
      return Vector3.new(1.672248125076294, 0.835624098777771, 0.835624098777771)
    end
    return antihitbox(...)
    end))
    
    local HitboxExpander = {HitBX = 3.15,HitBY = 3.15,HitBZ = 3.15,}
    for v, i in pairs(workspace:GetChildren()) do
      if i:FindFirstChild("HumanoidRootPart") then
        i.Head.Size = Vector3.new(HitboxExpander.HitBX, HitboxExpander.HitBY, HitboxExpander.HitBZ)
        i.Head.CanCollide = false
        i.Head.Color = Color3.fromRGB(255, 0, 10)
        i.Head.Material = "ForceField"
      end
    end
    game.ReplicatedStorage.Player.Head.Size = Vector3.new(HitboxExpander.HitBX, HitboxExpander.HitBY, HitboxExpander.HitBZ)
end)

local MyButton9 = MyButton7:AddButton('Advanced Hand', function()

local function setArmProperties(property, value)
local armParts = {"LeftUpperArm", "LeftLowerArm", "LeftHand","RightUpperArm", "RightLowerArm", "RightHand"}
for _, partName in ipairs(armParts) do game:GetService("Workspace").Ignore.FPSArms[partName][property] = value end
end
setArmProperties("Color", Color3.fromRGB(255, 0, 10)) setArmProperties("Material", "ForceField") setArmProperties("TextureID", "rbxassetid://9305457875")

end)

local MyButton10 = DevRightBox2:AddButton('No Grass', function()
sethiddenproperty(game.Workspace.Terrain, "Decoration", false)
end)

-- Button:AddTooltip
-- Arguments: ToolTip

MyButton:AddTooltip('Good for Blatant Mode!')
MyButton2:AddTooltip('Good for Blatant Mode!')
MyButton3:AddTooltip('Risky 50/50')
MyButton4:AddTooltip('Good for Legit/Blatant')
MyButton5:AddTooltip('FOR RAGE!!!')
MyButton6:AddTooltip('Shows other players')
MyButton7:AddTooltip('Safe %100')
MyButton8:AddTooltip('FOR LEGIT!!!')
MyButton9:AddTooltip('Safe %100 / Cool')
MyButton10:AddTooltip('No Grass more!')

-- Library functions
-- Sets the watermark visibility
Library:SetWatermarkVisibility(true)

-- Sets the watermark text
Library:SetWatermark('ZdReWare - Test Version v0.2')

Library.KeybindFrame.Visible = true; -- todo: add a function for this

Library:OnUnload(function()
    print('ZdReWare is UNLOADED!')
    Library.Unloaded = true
end)

-- UI Settings
local MenuGroup = Tabs['UI Settings']:AddLeftGroupbox('Menu')

-- I set NoUI so it does not show up in the keybinds menu
MenuGroup:AddButton('Unload', function() Library:Unload() end) 
MenuGroup:AddLabel('Menu bind'):AddKeyPicker('MenuKeybind', { Default = 'Instert', NoUI = true, Text = 'Menu keybind' }) 

Library.ToggleKeybind = Options.MenuKeybind -- Allows you to have a custom keybind for the menu

-- Addons:
-- SaveManager (Allows you to have a configuration system)
-- ThemeManager (Allows you to have a menu theme system)

-- Hand the library over to our managers
ThemeManager:SetLibrary(Library)
SaveManager:SetLibrary(Library)

-- Ignore keys that are used by ThemeManager. 
-- (we dont want configs to save themes, do we?)
SaveManager:IgnoreThemeSettings() 

-- Adds our MenuKeybind to the ignore list 
-- (do you want each config to have a different menu key? probably not.)
SaveManager:SetIgnoreIndexes({ 'MenuKeybind' }) 

-- use case for doing it this way: 
-- a script hub could have themes in a global folder
-- and game configs in a separate folder per game
ThemeManager:SetFolder('MyScriptHub')
SaveManager:SetFolder('MyScriptHub/specific-game')

-- Builds our config menu on the right side of our tab
SaveManager:BuildConfigSection(Tabs['UI Settings']) 

-- Builds our theme menu (with plenty of built in themes) on the left side
-- NOTE: you can also call ThemeManager:ApplyToGroupbox to add it to a specific groupbox
ThemeManager:ApplyToTab(Tabs['UI Settings'])

-- You can use the SaveManager:LoadAutoloadConfig() to load a config 
-- which has been marked to be one that auto loads!

Library:Notify("Status: Undetected 🟩",8)
Library:Notify("Made by voidzdre",8)
Library:Notify("This Version is TEST!",8)
