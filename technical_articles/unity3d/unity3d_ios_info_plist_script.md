[Up](index.md)

# 유니티 스크립트에서 iOS Info.plist 설정 방법.

애플의 iOS 심사는 앱이 미국법에 따라 암호 기술 유출 여부나 비트 코드 옵션들을 Info.plist에 설정하도록 요구 합니다. 이 옵션들은 XCODE에서 프로젝트를 열어서 설정 할 수도 있습니다. 그러나 유니티가 XCODE 프로젝트 파일을 생성하므로 유니티에서 빌드 때마다 XCODE에서 다시 수작업으로 수정하는 것은 시간을 버립니다.

유니티는 스크립트를 통해 Info.plist를 수정할 수 있게 도와 줍니다. 이 스크립트 파일은 macOS에 설치된 유니티 에디터를 통해 실행되므로 반드시 Editor 폴더 아래에 두어야 합니다.

```c#
using System.Collections.Generic;
using UnityEngine;
using UnityEditor;
using UnityEditor.Callbacks;

using UnityEditor.iOS.Xcode;


public static class XcodeOption
{

    [PostProcessBuild(999)]
    public static void OnPostProcessBuild( BuildTarget buildTarget, string path)
    {
        if(buildTarget == BuildTarget.iOS)
        {
            {
                string projectPath = path + "/Unity-iPhone.xcodeproj/project.pbxproj";

                PBXProject pbxProject = new PBXProject();
                pbxProject.ReadFromFile(projectPath);

                string target = pbxProject.TargetGuidByName("Unity-iPhone");            
                pbxProject.SetBuildProperty(target, "ENABLE_BITCODE", "NO");

                pbxProject.WriteToFile (projectPath);
            }

            {
                string infoPlistPath = path + "/Info.plist";

                PlistDocument plistDoc = new PlistDocument();
                plistDoc.ReadFromFile(infoPlistPath);
                if (plistDoc.root != null) {
                    plistDoc.root.SetBoolean("ITSAppUsesNonExemptEncryption", false);
                    plistDoc.root.SetString("CFBundleDisplayName", "MY APP NAME");
                    plistDoc.WriteToFile(infoPlistPath);
                }
                else {
                    Debug.LogError("ERROR: Can't open " + infoPlistPath);
                }
            }

            //ITSAppUsesNonExemptEncryption
        }
    }

}
```

