[Up](index.md)

# Windows Service Management

## 서비스 목록

현재 서비스 상태를 보려면 `services.exe`를 실행합니다. `services.exe`는 윈도우 기본 유틸리티 입니다.

## 서비스 등록

```powershell
sc [서버이름] create -binFile="서비스를 위해 작성된 앱 절대 경로"
```

## 서비스 삭제

```powershell
sc [서버이름] delete <서비스이름>
```

## 서비스 시작

```powershell
sc [서버이름] start <서비스이름>
```

또는

```powershell
net start <서비스이름>
```

## 서비스 중지

```powershell
sc [서버이름] stop <서비스이름>
```

또는

```powershell
net stop <서비스이름>
```

## 전체 서비스 조회

```powershell
sc [서버이름] query
```

## 이름으로 서비스 조회

```powershell
sc [서버이름] query <서비스이름>
```

## 참조

- [Sc.exe를 사용하여 Windows 서비스를 만드는 방법](https://support.microsoft.com/ko-kr/help/251192/how-to-create-a-windows-service-by-using-sc-exe)