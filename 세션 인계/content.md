# content 브랜치 세션 인계

최종 갱신: 2026-10-29

> **2026-10-29**: system `b765d27`(숙소·상인조합 UI 94~103, 마구간 매각·숙소 휴식 104·105, 퀘스트 106·107, 숙소 휴식 하루 흐름·휴식 중 이벤트 108·109 등)이 develop 경유로 fast-forward 병합+push됨(`18dc8a1..b765d27`). 세션 종료 시 작업 브랜치를 content로 전환. **content에서 진행할 작업은 아직 지시 없음 — 다음 세션 시작 시 확인.** system 쪽 미확인 2건(Field 상행 이벤트 Play 재확인, 골드 상자 인출 결제 Play)은 `system.md` 참조.

> **2026-10-15**: system `18dc8a1`(상행 대화·이벤트 공통 기반 87, 상행 대화 88, 마차·시설 내구도 91 A~C, 골드 HUD·팔레트·임시 보관 출발 경고 수정 등)이 develop 경유로 fast-forward 병합+push됨. content는 122커밋 뒤처져 있었다(2026-09-03 이후 첫 반영).
목적: `content` 브랜치에서 진행 중인 작업을 세션 간에 이어가기 위한 살아있는 문서. 세부 근거는 각 기획/설계/제작 문서가 우선이며, 이 문서는 "지금 어디까지 왔고 다음에 뭘 할지"만 담는다. 새 세션마다 새 파일을 만들지 않고 이 문서를 직접 수정/삭제/갱신한다.

---

## 1. 현재 상태 요약

`develop`에서 분기(battle 병합 이후). 이 브랜치에서 진행하던 작업 2건 **전부 기획→설계→제작→Unity 실전 검증→커밋→push 완료**:
1. 콘텐츠 데이터(용병/적 스탯, 방향성 지시 옵션)의 엑셀 임포트 파이프라인 전환(§3) — 커밋 `793a515`.
2. 상행 준비 UI 지도의 도시/경로 배치 영구 저장 + 엑셀 테이블화 + Id 전면 정수화(§6) — 커밋 `4e8b03e`.

두 커밋 모두 `origin/content`에 push 완료, 그리고 `develop`에 fast-forward 병합 후 `origin/develop`에도 push 완료(2026-09-03). **이 문서 기준으로 `content` 브랜치의 활성 작업은 없다** — 다음 작업은 `system` 브랜치로 이어간다(그쪽 세션 인계 문서 참고/신설).

## 2. 남은 잡음 (급하지 않음)

1. `Assets/_Recovery/`(Unity 자동복구 씬 백업), `BootstrapDefaultSceneOpener.cs`, `ProjectSceneSettingsInstaller.cs`가 여전히 미커밋 상태로 남아있다 — 데이터 테이블/도시 지도 작업과 무관해 두 커밋 범위에서 의도적으로 제외했다(§5 참고). `_Recovery`는 커밋 대상인지 자체가 불명확 - 사용자에게 필요 여부 확인 필요.
2. §4의 나머지 미정 사항(교차 FK 검증, 상시 검증기 신설 여부, xlsx Git 관리 세부 방침)은 급하지 않음 - 필요 시 별도 기획으로.

## 3. 데이터 테이블 엑셀화 v2 — 제작+Unity 검증+커밋 완료

기획: [Docs/기획/14-2026-09-02-데이터_테이블_엑셀화_기획.md](../기획/14-2026-09-02-데이터_테이블_엑셀화_기획.md) §6
설계: [Docs/설계/18-2026-09-03-데이터_테이블_v2_스키마_아키텍처.md](../설계/18-2026-09-03-데이터_테이블_v2_스키마_아키텍처.md)

**핵심 스키마 결정**(기획 14번 §6): (1) Data 시트(수치/Id)와 String 시트(Id→한국어 텍스트) 분리, (2) 도메인별 워크북+폴더 분리(`Character/`·`Enemy/`·`Tactics/`), (3) enum 9종에 명시적 Id를 고정하고 데이터 시트 PK로 사용(게임 로직 분기는 enum 그대로, UI 출력 텍스트만 String 시트 조회로 분리).

**9개 enum에 명시적 Id 부여**(`MercenaryClass`/`EnemyType`/`RoleGroup`/`TargetPriority`/`LocalPositioning`/`SelfPreservation`/`EnemyRecognitionType`/`ActivityRadiusPreset`/`PursuitPreset`, 전부 1부터 시작 — 0은 미초기화 감지용으로 비워둠. 구체적 값은 설계 18번 §3 표) — 각 enum 파일 수정.

**신규 파일**:
- `Assets/Scripts/Core/Battle/LocalizedStringEntry.cs` — Id→Ko 공용 엔트리 struct
- `Assets/Scripts/Core/Battle/Stats/CharacterStringsTableAsset.cs`, `Enemy/EnemyStringsTableAsset.cs`, `Tactics/PartyTacticsPolicyStringsTableAsset.cs`, `Tactics/RoleGroupTacticsStringsTableAsset.cs` — String 조회 에셋 4종
- `Assets/Scripts/Editor/EnemyStatsTableImporter.cs`, `PartyPolicyTableImporter.cs`, `RoleGroupTacticsTableImporter.cs`(v1 `TacticsTableImporter.cs` 대체), `TableImportAll.cs`(Import All 편의 메뉴)
- `Assets/Table/Character/CharacterStats.xlsx`, `Assets/Table/Enemy/EnemyStats.xlsx`, `Assets/Table/Tactics/PartyPolicy.xlsx`, `Assets/Table/Tactics/RoleGroupTactics.xlsx` — v1 임포트 결과(`.asset` YAML)에서 값을 그대로 이전(창작 없음). **주의**: 기획 14번 §6.3의 `RoleGroupSelfPreservationOptions` 시트명이 Excel 31자 제한을 넘어 `RoleGroupSelfPreserveOptions`로 축약(설계 문서에 없던 구현 단계 보정, `RoleGroupTacticsTableImporter.cs` 주석 참고).

**삭제**: `Assets/Scripts/Editor/TacticsTableImporter.cs`(+`.meta`) — `RoleGroupTacticsTableImporter.cs` + `PartyPolicyTableImporter.cs`로 분리 대체.

**수정**: `EditorTableReader.cs`(`ParseEnum`을 이름 매칭→Id 매칭으로 교체, `SetEnumValue<TEnum>` 헬퍼 추가), `CharacterStatsTableImporter.cs`(Character 워크북만 담당하도록 축소), `CharacterStatsTableAsset.cs`/`EnemyStatsTableAsset.cs`(`DisplayName` 필드 제거), `RoleGroupTacticsCatalogAsset.cs`/`PartyTacticsPolicyCatalogAsset.cs`(`DisplayLabel` 필드 제거), `TacticsPanel.cs`(String 에셋 2종 배선+라벨 조회로 전환, 조회 실패 시 "값 없음" 폴백), `TableAutoImportOnPlay.cs`(임포터 4개 호출), `ManagerHierarchyInstaller.cs`/`Editor/Debug/BattleTestSceneInstaller.cs`(String 에셋 2종 자동 배선 추가).

**구현 중 발견한 선결 버그(설계 문서에 없던 것, 설계 18번 §2가 예견은 했으나 실제 수정은 이번 제작에서)**: `SerializedProperty.enumValueIndex`는 enum의 "선언 순서"를 받지 멤버에 박은 실제 정수값을 받지 않는다 — v1은 선언 순서=값이라 안 드러났지만 v2가 1부터 시작하는 Id를 부여하면서 어긋난다. 4개 임포터의 모든 enum 필드 쓰기를 `EditorTableReader.SetEnumValue`(내부적으로 `intValue` 사용)로 교체해 해결.

**정리됨**: `Assets/Table/CharacterStats.xlsx`/`Tactics.xlsx`(v1 워크북, 아무 임포터도 안 읽던 고아 파일) — 설계 18번 §10대로 커밋 전에 삭제 완료.

### 3.1 v1 산출물 (v2로 대체됨, 참고용)

기획 [14번](../기획/14-2026-09-02-데이터_테이블_엑셀화_기획.md) §1~§5, 설계 [17번](../설계/17-2026-09-02-데이터_테이블_임포트_아키텍처.md). `EditorTableReader`/`GetOrCreateAsset<T>`/get-or-create 임포터 패턴/Provider 어댑터 구조(`TableBattleUnitStatProvider`/`TableEnemyTypeCompositionProvider`, 인터페이스 무변경) 자체는 v2도 그대로 계승 — 재론 불필요. Placeholder 2종(`PlaceholderBattleUnitStatProvider`/`PlaceholderEnemyTypeCompositionProvider`) 삭제, `ExcelDataReader` 플러그인 배치(`Assets/Plugins/Editor/ExcelDataReader/`)는 v1에서 이미 완료된 채 유지됨.

## 4. 남은 미정 사항 (카테고리별)

### 4.1 급하지 않음, 필요 시 별도 기획으로 (설계 18번 §11)
- 교차 파일 FK(`MercenaryRoleGroupMap.CharacterId`가 `CharacterStats.xlsx`의 Id를 실제로 가리키는지) 검증 여부 — 현재는 검증 안 함.
- Id 버전 상시 검증기(`TacticsCatalogValidator` 대응) 신설 여부.
- 임포트 시 기존 인스펙터 수동 데이터가 엑셀로 덮어써질 때 사용자 안내 방식(설계 17번 §8-1).

### 4.2 결정됨
- `Assets/Table/*.xlsx` 바이너리 파일 Git 관리 방침(기획 14번 §5-4) — **바이너리 그대로 커밋하기로 확정**(2026-09-03). 근거: 파일이 작고 변경 빈도가 낮아 LFS/textconv 도입 비용 대비 실익이 적음. diff 가독성이 실제로 아쉬워지면 그때 textconv 드라이버 추가 검토.

## 5. 그 외 완료 작업 (데이터 테이블과 무관, 미커밋 상태로 남아있음)

- **`BootstrapDefaultSceneOpener.cs`** — 유니티 에디터 프로세스가 새로 실행될 때마다(재컴파일 도중 도메인 리로드에는 반응 안 함, `SessionState`로 판별) `Bootstrap.unity`를 자동으로 연다.
- **`ProjectSceneSettingsInstaller.cs`** — 처음엔 "Play Mode Start Scene을 Bootstrap으로 고정"하는 기능을 만들었으나, 사용자 의도가 "에디터 기본 오픈 씬"이었음이 확인되어 **원상복구용**(`Tools/Game/Clear Play Mode Start Scene`)으로 용도가 바뀌었다 — 사용자가 이미 실행했는지 확인 필요할 수 있음(Play가 다시 "현재 열린 씬"부터 시작하는지).

## 6. 도시 지도 데이터 저장 — v1(SO 직접저장) 완료 → v2(엑셀 테이블화+Id 전면 정수화) 제작+검증+커밋 완료

**v2(엑셀 테이블화)**: 기획 [15번 §7~§8](../기획/15-2026-09-03-도시_지도_데이터_저장_기획.md), 설계 [20번](../설계/20-2026-09-03-도시_지도_데이터_엑셀테이블화_아키텍처.md). v1 방식("저장" 버튼이 SO 에셋에 직접 씀)을 "드래그+엑셀 둘 다 유효, 엑셀이 단일 진실 소스"로 전환. Id 전면 정수화 이후 **Unity 컴파일 성공 확인됨**(2026-09-03). **기능 검증 전부 통과**(사용자 확인, 2026-09-03): 지도 표시/경로 추가/String 테이블/재시작 복원/신규 도시 String 자리표시자 자동 추가. 저장 도중 발생한 `IOException`(엑셀이 파일을 열어둔 상태)은 코드 버그가 아니라 파일 잠금 문제로 진단, `TripCityMapPersistence.Save`에 안내 메시지를 남기는 방어 처리 추가 후 재검증까지 완료. **커밋 완료**(`4e8b03e`), `content`/`develop` 양쪽 push 완료.

**Id 전면 정수화(2026-09-03, 사용자가 "유지보수 악영향" 우려로 범위 확대 지시)**: 최초엔 Id 정수화를 "엑셀/`TripCityMapAsset` 경계로만" 좁혀 잡았으나(기획 §8.2 최초 결정), 사용자가 그 경계 변환 자체가 유지보수 부담이라 판단해 **`TripOriginDestinationAssigner`/`ITripOriginDestinationReader`/`ITripOriginDestinationAssigner`/`ITripRouteReader`/`ITripLocationInfo`/`PlaceholderTripLocationInfo`(전부 "정식", `Core/UI/Trip/`, 지금까지 이 작업에서 손대지 않기로 선 그어뒀던 영역)까지 포함한 전면 정수화**로 재확정(기획 §8.2 재정정, 설계 20번 §9). `string`→`int`(미배정 상태는 `int?`)로 아래 15개 파일을 수정:
- 정식: `ITripOriginDestinationReader.cs`, `ITripOriginDestinationAssigner.cs`, `TripOriginDestinationAssigner.cs`(전체 재작성), `ITripRouteReader.cs`, `ITripLocationInfo.cs`, `PlaceholderTripLocationInfo.cs`, `TripCityMapAsset.cs`
- 디버그: `TripCity.cs`, `ITripCityRepository.cs`, `InMemoryTripCityRepository.cs`(순번 재개 로직 단순화), `ITripRouteRepository.cs`, `InMemoryTripRouteRepository.cs`(문자열 키→`(int,int)` 튜플 키로 최적화), `TripDebugCityMarkerView.cs`, `MoveCityDragBehavior.cs`, `DrawRoadDragBehavior.cs`, `TripMapInteractionCoordinator.cs`(전체 재작성)
- `DEBUG_FEATURES.md`의 "정식 코드는 안 건드린다" 경계 서술도 이번 예외를 반영해 정정.

**도시 이름/설명 String 시트 신설**: `TripCityStringsTableAsset.cs`(신규, `Core/UI/Trip/`) - `TripCityStrings` 시트(`Id, Name, Description`)를 임포트. `TripMapInteractionCoordinator.BuildLocationInfo`가 이 데이터를 우선 쓰고, 없으면 기존 자동생성 placeholder로 폴백하도록 연동 완료.

**저장 시 String 시트 보존**: `TripCityMapPersistence.Save`가 기존 워크북을 열어 `TripCities`/`TripRoutes`만 재생성하고 `TripCityStrings`(사람이 직접 입력하는 시트)는 손대지 않도록 수정 - 안 그러면 저장 버튼 클릭마다 사람이 입력한 이름이 날아갈 뻔했다(설계 20번 §9.5).

**시드 워크북 재생성**: `debug-city-N` 문자열 Id를 순수 정수 N으로 변환해 `Assets/Table/Trip/TripCityMap.xlsx` 재생성(값 손실 없음), 빈 `TripCityStrings` 시트 포함.

**신규 파일**: `Core/UI/Trip/TripCityMapCoordinateConverter.cs`, `Core/UI/Trip/TripCityStringsTableAsset.cs`, `Assets/Scripts/Editor/TripCityMapTableImporter.cs`, `Assets/Plugins/Editor/ClosedXML/`(DLL **8개**, `Microsoft.Bcl.HashCode`는 Auto Reference 꺼서 포함 - §2 참고), `Assets/Table/Trip/TripCityMap.xlsx`.

**이동**: `TripCityMap.asset`을 `Assets/Table/Trip/` → `Assets/Prefabs/ScriptableObejct/`로 되돌림(다른 컴파일된 SO 산출물과 같은 위치로 통일).

### v1 — SO 에셋 직접 저장 (아래 내용, 제작+버그수정 완료, Unity 미검증)

기획: [Docs/기획/15-2026-09-03-도시_지도_데이터_저장_기획.md](../기획/15-2026-09-03-도시_지도_데이터_저장_기획.md)
설계: [Docs/설계/19-2026-09-03-도시_지도_데이터_저장_아키텍처.md](../설계/19-2026-09-03-도시_지도_데이터_저장_아키텍처.md)

**신규 파일**: `Core/Debug/Trip/ITripCityReader.cs`, `Core/Debug/Trip/TripCityMapPersistence.cs`, `Core/UI/Trip/TripCityMapAsset.cs`.
**수정 파일**: `ITripCityRepository.cs`/`InMemoryTripCityRepository.cs`(`AddWithId` 추가), `ITripRouteRepository.cs`/`InMemoryTripRouteRepository.cs`(`GetAllRoutes` 추가), `TripMapInteractionCoordinator.cs`(저장 버튼 배선+`EnsureSavedMapLoaded()`), `TripPanel.cs`(`debugMapSaveButton` 필드/배선 + `Open()`에서 불러오기 호출), `TripUIElementIds.cs`(`DebugMapSaveButton` 상수), `Editor/HubSceneInstaller.cs`(저장 버튼 UI 생성, 앵커 0.64~0.78), `DEBUG_FEATURES.md`(신규 파일/연동 지점 반영).

**제작 직후 사용자가 겪은 버그(2026-09-03) — 수정 완료**: "저장된 내용이 플레이 종료 이후에도 불러와지지 않는다"고 보고 — 확인해보니 저장 자체는 정상이었다(`.asset` 파일에 도시 10개/경로 9개가 실제로 저장돼 있었음). 원인은 **불러오기 시점**: 최초 구현은 `Bind()`(등록 시점, 패널이 아직 비활성) 끝에서 곧바로 불러왔는데, `TripMapView.Content`/`Viewport`는 패널이 최초로 활성화(`TripPanel.Open()`)되기 전까지 `null`이라(그 클래스 기존 주석에 이미 명시돼 있던 제약) 마커가 부모 없이 생성돼 화면에 안 보였다 — 데이터는 내부적으로 정상 복원됐지만 시각적으로 확인이 안 됐던 것. **수정**: `TripMapInteractionCoordinator`에 `EnsureSavedMapLoaded()`(세션당 1회 가드)를 만들고, `TripPanel.Open()`의 `panelRoot.SetActive(true)` 직후에 호출하도록 변경.

**저장 위치 변경(사용자 요청)**: `Assets/Prefabs/ScriptableObejct/TripCityMap.asset` → **`Assets/Table/Trip/TripCityMap.asset`**(엑셀 워크북들과 같은 `Assets/Table/` 도메인 폴더 관례에 통일). 기존에 저장돼 있던 도시 10개/경로 9개 데이터는 파일 이동으로 그대로 유지됨(재저장 불필요).

**배경**: 상행 준비 UI 지도에 이미 디버그 도시/경로 배치 도구(기획 03/04번, `Assets/Scripts/Core/Debug/Trip/`)가 있으나, 데이터가 전부 인메모리(`InMemoryTripCityRepository`/`InMemoryTripRouteRepository`)라 앱 재시작마다(매 플레이 테스트) 사라진다. 사용자가 이 배치 결과를 영구 저장하는 버튼을 요청.

**기획 확정 사항**: (1) 저장+자동 불러오기 모두 포함 — 저장만으론 "매번 초기화" 문제가 안 풀림. (2) 좌표+연결만 저장, 도시 이름은 제외(자동 Id뿐, 이름 UI는 후속 과제). (3) 기존 디버그 버튼 4개 옆 빈 구간(0.64~0.78)에 저장 버튼 배치, 겹침 방지. (4) 임시 디버깅용 기능임을 코드 레벨로 명시(`#if UNITY_EDITOR`, `Debug` 접두사, `DEBUG_FEATURES.md` 등재).

**설계 확정 사항(19번)** — 기획 §6의 미정 사항 5개 전부 해소:
1. **전체 조회 API 신설**: `ITripCityReader`(신규, `Core/Debug/Trip/`) + `ITripRouteRepository.GetAllRoutes()`(신규) — 정식 `ITripRouteReader`는 건드리지 않음(ISP). `DEBUG_FEATURES.md`가 원래도 언급만 하고 실체가 없던 `ITripCityReader.cs`를 이번에 실제로 채움.
2. **저장 포맷**: `TripCityMapAsset`(신규, `Core/UI/Trip/` - `#if UNITY_EDITOR` 밖, 정식 데이터로 관리). `Assets/Prefabs/ScriptableObejct/TripCityMap.asset`.
3. **불러오기는 에디터 전용으로 확정**: 저장/불러오기 로직(`TripCityMapPersistence`, `Core/Debug/Trip/`) 전체가 `#if UNITY_EDITOR` — 정식 빌드 로드는 범위 밖(이번 기능이 "임시 디버깅용"이라는 기획 확정과 일치).
4. **도시 Id 안정성**: `ITripCityRepository.AddWithId(id, pos)` 신설 - 저장된 Id를 그대로 보존하고, `debug-city-N` 패턴이면 내부 순번 카운터를 N+1로 재개해 충돌 방지.
5. **UI 배치/피드백**: §7~§8에서 코드 스케치까지 확정(`HubSceneInstaller`/`TripPanel`/`TripMapInteractionCoordinator` 배선 변경 지점 전부 명시). 피드백은 `Debug.Log`(임포터 완료 로그와 동일 수준).

**제작 시 주의**: `EditorTableReader.GetOrCreateAsset<T>`는 재사용 불가(어셈블리 경계 - `Assets/Scripts/Editor/`는 에디터 전용 어셈블리, `Core/Debug/Trip/`은 일반 어셈블리라 참조 방향이 막힘) - `TripCityMapPersistence`에 자체 get-or-create 로직을 짧게 둠(의도된 중복).
