Get runtime version:
(dir (Get-Command dotnet).Path.Replace('dotnet.exe', 'shared\Microsoft.NETCore.App')).Name `2.2.2`
Get SDK Version:
(dir (Get-Command dotnet).Path.Replace('dotnet.exe', 'sdk')).Name `2.2.104`
