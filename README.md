# Ultravnc
UltraVNC build steps for Windows 10 x64 and Visual Studio 2019

1. Clone this repo

2. Download NASM (589KB) and extract it
 - direct link: https://www.nasm.us/pub/nasm/releasebuilds/2.14.03rc2/win64/nasm-2.14.03rc2-win64.zip
 - Edit Environment Variables and add: NASM_DIR = D:\work\NASM\  (this is the root path which contains the "nasm.exe" binary)
 - libjpeg-turbo-win_VC2019.vcxproj will look for it via "%25NASM_DIR%25"

3. Download DirectX Software Development Kit (DXSDK_Jun10.exe, 571.6MB) and install or only extract the "Include" and "Lib" folders
 - link: https://www.microsoft.com/en-us/download/details.aspx?id=6812
 - direct link: https://download.microsoft.com/download/A/E/7/AE743F1F-632B-4809-87A9-AA1BB3458E31/DXSDK_Jun10.exe 
 - Edit Environment Variables and add: DXSDK_DIR = D:\work\DXSDK\  (this is the root path which contains the "Include" and "Lib" folders)
 - vncviewer_VC2019.vcxproj will look for it via "%25DXSDK_DIR%25\Include" and "%25DXSDK_DIR%25\Lib\x86" respectivelly "%25DXSDK_DIR%25\Lib\x64"

4. If not created, create "C:\uvnc\64\xp\" and "C:\uvnc\32\xp\" which will contain the built x64 respectivelly x86 binaries
 - there are many Release configurations which have PostBuildEvent to copy resulted binaries in these folders

5. If not have one, generate Windows signing certificate (see more at https://help.gamesalad.com/windows-cookbook/publishing/windows-publishing-cordova/4-sign-app/)
 - create Mine.pvk (private key file, as example use password=123) and Mine.cer (public SSL certification key):
   >  makecert.exe -r -sv Mine.pvk -n CN="WinOSHub" Mine.cer
 - create Mine.spc (Software Publisher Certificate):
   >  cert2spc.exe Mine.cer Mine.spc
 - Convert pvk+cer into Mine.pfx (Personal Exchange Format, fully encrypted, where 123 is the password used in makecert step above):
   >  pvk2pfx.exe -pvk Mine.pvk -pi 123 -spc Mine.spc -pfx Mine.pfx -po 456
 - Add the new certificate to the system (as administrator):
   >  certmgr.exe -add Mine.cer -s -r localMachine ROOT
   >  certmgr.exe -add Mine.cer -s -r localMachine TRUSTEDPUBLISHER

6. To sign the resulted builds with the generated key, Release configurations have a PostBuildEvent with %25SignWithWindowsCertificate%25 "$(OutDir)$(TargetName)$(TargetExt)" command.
 - Edit Environment Variables and add: SignWithWindowsCertificate = "C:\Program Files (x86)\Windows Kits\10\bin\10.0.18362.0\x64\signtool.exe" sign /f D:\work\WindowsCertificate\Mine.pfx /p 456 /v
 - where Mine.pfx is the created certificate and 456 is the used password (change accordingly if different)

7. Open ultravnc\winvnc\winvnc_VC2019.sln and build needed X64 or x86 Release configurations
 - winvnc.exe and uvnc_settings.exe should be found into C:\uvnc\64\xp\ or C:\uvnc\32\xp\

8. Open ultravnc\vncviewer\vncviewer_VC2019.sln and build needed X64 or x86 Release configurations
 - vncviewer.exe should be found into C:\uvnc\64\xp\ or C:\uvnc\32\xp\

