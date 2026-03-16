# Thegiver
Steal a bzh script Only Insta tp base and speed boost work (for french)
 the script

 local Pl,RS,UIS,TS=game:GetService("Players").LocalPlayer,game:GetService("RunService"),game:GetService("UserInputService"),game:GetService("TweenService")
local C=Color3.fromRGB;local sg=Instance.new("ScreenGui",Pl.PlayerGui);sg.ResetOnSpawn=false
local colors={C(255,0,100),C(255,150,0),C(255,255,0),C(0,255,100),C(0,150,255),C(150,0,255)}
local ci=1;local ct=0
local f=Instance.new("Frame",sg);f.Size=UDim2.new(0,220,0,380);f.Position=UDim2.new(0.5,-110,0.02,0);f.BackgroundColor3=C(5,5,5);f.BorderSizePixel=0;Instance.new("UICorner",f).CornerRadius=UDim.new(0,12)
local fStroke=Instance.new("UIStroke",f);fStroke.Thickness=2
local tl=Instance.new("TextLabel",f);tl.Size=UDim2.new(1,-40,0,32);tl.BackgroundColor3=C(20,20,20);tl.Text="🌈 Brainrot Hub";tl.Font=Enum.Font.GothamBlack;tl.TextSize=15;tl.TextColor3=C(255,255,255);tl.BorderSizePixel=0;Instance.new("UICorner",tl).CornerRadius=UDim.new(0,12)
local tlStroke=Instance.new("UIStroke",tl);tlStroke.Thickness=1.5
RS.Heartbeat:Connect(function(dt)ct=ct+dt;if ct>=1 then ct=0;ci=ci%#colors+1;fStroke.Color=colors[ci];tlStroke.Color=colors[ci]end end)
local min=Instance.new("TextButton",f);min.Size=UDim2.new(0,30,0,20);min.Position=UDim2.new(1,-35,0,6);min.BackgroundColor3=C(40,40,40);min.Text="—";min.TextColor3=C(180,180,180);min.Font=Enum.Font.GothamBlack;min.TextSize=11;min.BorderSizePixel=0;Instance.new("UICorner",min).CornerRadius=UDim.new(0,6)
local mind=false;min.MouseButton1Down:Connect(function()mind=not mind;if mind then TS:Create(f,TweenInfo.new(0.3),{Size=UDim2.new(0,220,0,36)}):Play();min.Text="+"else TS:Create(f,TweenInfo.new(0.3),{Size=UDim2.new(0,220,0,380)}):Play();min.Text="—"end end)
local drag,ds,sp=false,nil,nil;tl.InputBegan:Connect(function(i)if i.UserInputType==Enum.UserInputType.Touch then drag=true;ds=i.Position;sp=f.Position end end);tl.InputEnded:Connect(function(i)if i.UserInputType==Enum.UserInputType.Touch then drag=false end end);UIS.InputChanged:Connect(function(i)if drag and i.UserInputType==Enum.UserInputType.Touch then local d=i.Position-ds;f.Position=UDim2.new(sp.X.Scale,sp.X.Offset+d.X,sp.Y.Scale,sp.Y.Offset+d.Y)end end)
local scr=Instance.new("ScrollingFrame",f);scr.Size=UDim2.new(1,-8,1,-40);scr.Position=UDim2.new(0,4,0,38);scr.BackgroundTransparency=1;scr.BorderSizePixel=0;scr.ScrollBarThickness=5;scr.ScrollingEnabled=true;scr.ScrollingDirection=Enum.ScrollingDirection.Y;scr.CanvasSize=UDim2.new(0,0,0,0);scr.ClipsDescendants=true
local lay=Instance.new("UIListLayout",scr);lay.Padding=UDim.new(0,5);lay.HorizontalAlignment=Enum.HorizontalAlignment.Center;lay.SortOrder=Enum.SortOrder.LayoutOrder
local pad=Instance.new("UIPadding",scr);pad.PaddingTop=UDim.new(0,5);pad.PaddingBottom=UDim.new(0,5)
local tso=0;local btnStrokes={}
local function mkB(t)local b=Instance.new("TextButton",scr);b.Size=UDim2.new(0.92,0,0,30);b.BackgroundColor3=C(15,15,15);b.Text=t;b.Font=Enum.Font.GothamBold;b.TextSize=11;b.TextColor3=C(255,255,255);b.BorderSizePixel=0;b.LayoutOrder=tso;tso=tso+1;Instance.new("UICorner",b).CornerRadius=UDim.new(0,8);local s=Instance.new("UIStroke",b);s.Thickness=1.5;table.insert(btnStrokes,s);return b end
local function mkSection(t)local s=Instance.new("TextLabel",scr);s.Size=UDim2.new(0.92,0,0,20);s.BackgroundColor3=C(20,20,20);s.Text=t;s.Font=Enum.Font.GothamBlack;s.TextSize=11;s.TextColor3=C(255,255,255);s.BorderSizePixel=0;s.LayoutOrder=tso;tso=tso+1;Instance.new("UICorner",s).CornerRadius=UDim.new(0,6) end
mkSection("── TP ──")
local bSetPos=mkB("📍 Set Position Base")
local bTP=mkB("🏠 Insta TP Base")
local bTPAvant=mkB("⚡ TP En Avant")
mkSection("── MOVE ──")
local bFly=mkB("✈ Fly")
local bSpd=mkB("🚀 Speed")
mkSection("── VISION ──")
local bESP=mkB("👁 ESP Brainrots")
lay.Changed:Connect(function()scr.CanvasSize=UDim2.new(0,0,0,lay.AbsoluteContentSize.Y+16)end)
local bct=0;RS.Heartbeat:Connect(function(dt)bct=bct+dt;if bct>=1 then bct=0;for _,s in pairs(btnStrokes)do s.Color=colors[ci]end end end)
local function mkFB(t,x,y)local b=Instance.new("TextButton",sg);b.Size=UDim2.new(0,50,0,50);b.Position=UDim2.new(x,0,y,0);b.BackgroundColor3=C(5,5,5);b.BackgroundTransparency=0.2;b.Text=t;b.TextColor3=C(255,255,255);b.Font=Enum.Font.GothamBlack;b.TextSize=14;b.BorderSizePixel=0;Instance.new("UICorner",b).CornerRadius=UDim.new(0,10);return b end
local bU,bD=mkFB("▲",0.82,0.65),mkFB("▼",0.82,0.8)
local held={};bU.MouseButton1Down:Connect(function()held["U"]=true end);bU.MouseButton1Up:Connect(function()held["U"]=false end);bD.MouseButton1Down:Connect(function()held["D"]=true end);bD.MouseButton1Up:Connect(function()held["D"]=false end)
local on,conns={},{}
local savedPos=nil
local function getH()return Pl.Character and Pl.Character:FindFirstChild("HumanoidRootPart")end
local function getHum()return Pl.Character and Pl.Character:FindFirstChildOfClass("Humanoid")end
local function findBrainrots()
	local h=getH();if not h then return {} end
	local list={}
	for _,obj in ipairs(workspace:GetDescendants())do
		if obj:IsA("Model") and obj~=Pl.Character then
			local hrp=obj:FindFirstChild("HumanoidRootPart")
			local hum=obj:FindFirstChildOfClass("Humanoid")
			if hrp and hum then
				local d=(hrp.Position-h.Position).Magnitude
				if d<100 then
					local prompt=nil
					for _,p in ipairs(obj:GetDescendants())do if p:IsA("ProximityPrompt")then prompt=p;break end end
					if prompt then table.insert(list,{model=obj,hrp=hrp,prompt=prompt,dist=d})end
				end
			end
		end
	end
	table.sort(list,function(a,b)return a.dist<b.dist end)
	return list
end
local function tog(k,b,fn)on[k]=not on[k];b.BackgroundColor3=on[k] and C(40,40,40)or C(15,15,15);fn(on[k])end
-- SET POSITION BASE
bSetPos.MouseButton1Down:Connect(function()
	local h=getH();if h then
		savedPos=h.Position
		bSetPos.Text="📍 Base sauvée !"
		bSetPos.TextColor3=C(0,255,100)
		task.delay(1.5,function()bSetPos.Text="📍 Set Position Base";bSetPos.TextColor3=C(255,255,255)end)
	end
end)
-- INSTA TP BASE (pendant que tu portes le brainrot)
bTP.MouseButton1Down:Connect(function()
	local h=getH()
	if h and savedPos then
		h.CFrame=CFrame.new(savedPos+Vector3.new(0,3,0))
	else
		bTP.Text="❌ Set une base d'abord!"
		task.delay(1.5,function()bTP.Text="🏠 Insta TP Base"end)
	end
end)
-- TP EN AVANT (pour entrer dans les bases)
bTPAvant.MouseButton1Down:Connect(function()
	local h=getH();if not h then return end
	local cam=workspace.CurrentCamera
	local dir=cam.CFrame.LookVector
	h.CFrame=CFrame.new(h.Position+dir*20)
end)
-- FLY
bFly.MouseButton1Down:Connect(function()tog("fly",bFly,function(v)local h=getH();local hum=getHum();if not h or not hum then return end;if v then hum.PlatformStand=true;conns.fly=RS.Heartbeat:Connect(function()local h2=getH();local hum2=getHum();if not h2 or not hum2 then return end;local md=hum2.MoveDirection;h2.AssemblyLinearVelocity=Vector3.new(md.X*80,(held["U"]and 50 or held["D"]and-50 or 1),md.Z*80)end)else if conns.fly then conns.fly:Disconnect();conns.fly=nil end;hum.PlatformStand=false;h.AssemblyLinearVelocity=Vector3.zero end end)end)
-- SPEED
bSpd.MouseButton1Down:Connect(function()tog("spd",bSpd,function(v)local hum=getHum();if hum then hum.WalkSpeed=v and 100 or 16 end end)end)
-- ESP
bESP.MouseButton1Down:Connect(function()tog("esp",bESP,function(v)if v then conns.esp=RS.Heartbeat:Connect(function()local list=findBrainrots();for _,item in ipairs(list)do local bb=item.hrp:FindFirstChild("BESP") or Instance.new("BillboardGui",item.hrp);bb.Name="BESP";bb.Size=UDim2.new(0,150,0,30);bb.StudsOffset=Vector3.new(0,4,0);bb.AlwaysOnTop=true;local lbl=bb:FindFirstChild("l") or Instance.new("TextLabel",bb);lbl.Name="l";lbl.Size=UDim2.new(1,0,1,0);lbl.BackgroundTransparency=1;lbl.Text="🧠 "..item.model.Name.." "..math.floor(item.dist).."m";lbl.TextColor3=colors[ci];lbl.Font=Enum.Font.GothamBlack;lbl.TextSize=12 end end)else if conns.esp then conns.esp:Disconnect();conns.esp=nil end;for _,o in ipairs(workspace:GetDescendants())do if o.Name=="BESP"then o:Destroy()end end end end)end)


or


loadstring(game:HttpGet(https://gist.githubusercontent.com/silverpoints2s3-web/497d0b54782331eb70fad687034b939b/raw/efc8ece4e82714b1335c28ce442af4e08c03324f/gistfile1.txt"))()
