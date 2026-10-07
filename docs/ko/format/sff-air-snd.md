# .sff / .air / .snd와 PAMSDK_Unity

세 파일 모두 **일반 JSON 텍스트**(UTF-8, 들여쓰기 포맷)이며 Unity/엔진 측에서 아무 JSON 라이브러리로 바로 역직렬화 가능. 동봉된 순수 데이터 SDK: 저장소 `Tools/FrameEventEditor.SDK/PAMSDK_Unity.cs`(단일 파일, 메서드 없음, 의존성 없음; 배포 패키지 내 위치는 `SDK/PAMSDK_Unity.cs`).

## .sff(스프라이트/아틀라스)

- `baseMap` / `normalMap`: 아틀라스 PNG **바이트**. Newtonsoft에서 `byte[]`는 **base64 문자열**로 직렬화.
- 스프라이트 메타데이터에 아틀라스 내 사각형 포함; **`SpriteMeta.rect`의 y축은 아래→위**(Unity 관례), 엔진 측에서 뒤집기 주의.
- 팔레트는 독립 항목(RGBA).

## .air(액션과 프레임 이벤트)

- 액션(action) = 프레임 시퀀스; 각 프레임에 여러 종류의 이벤트 부착 가능.
- **이벤트 다형성 판별 = `"type"` 클래스 이름 필드**:

```json
{ "type": "AttackEvent", …공격 박스 필드… }
```

  5종: `SpriteEvent`(표시) / `CollisionEvent`(피격 박스) / `BodyEvent`(바디 박스) / `AttackEvent`(공격 박스) / `SoundEvent`(효과음). 클래스 이름으로 대응 이벤트 클래스를 인스턴스화하고 나머지 필드를 채움.

## .snd(효과음)

- `byte[]`(WAV 바이트)는 **Unity JsonUtility의 숫자 배열 포맷**(`[82,73,70,70,…]`) 사용, **base64 아님** — .sff의 byte[]와 기준이 다름, 필드가 속한 파일에 따라 구분해 파싱.

## 주의 사항

- **MUGEN의 `.air`/`.sff`/`.snd`와 호환되지 않음**: 확장자가 같은 것은 순전히 우연 — 본 도구의 3종 세트는 자체 JSON 포맷이며, MUGEN 엔진은 읽을 수 없고 이 도구도 MUGEN 소재 파일을 읽을 수 없음.
- 클래스 이름이 프로젝트와 충돌하면 자유롭게 변경 가능, JSON `"type"` 판별값은 불변.
- 세 파일은 편집기가「같은 폴더 같은 이름 3종 세트」로 한꺼번에 저장(Ctrl+S).
- **Unity 임포트**: `.sff/.air/.snd` 확장자는 Unity가 인식하지 못함(TextAsset으로 들어오지 않음). 편집기의 다른 이름으로 저장에서「저장 형식」을 **JSON 캐릭터(Unity)**로 선택하면 `캐릭터.sff.json/.air.json/.snd.json` 3종이 됨 — TextAsset의 `text` 필드를 읽어 Newtonsoft + PAMSDK_Unity 데이터 클래스로 넘기면 됨.
- 포맷은 `Tools/FrameEventEditor.SDK/check_fields.py` 장부로 대조; 게임 측 Mugen.cs 필드 변경 시 SDK에 동기화.
