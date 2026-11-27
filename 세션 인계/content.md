# content 브랜치 세션 인계

최종 갱신: 2026-11-27(공격 애니메이션 제작 완료)

목적: `content` 브랜치에서 진행 중인 작업을 세션 간에 이어가기 위한 살아있는 문서. 세부 근거는 각 기획/설계/제작 문서가 우선이며, 이 문서는 "지금 어디까지 왔고 다음에 뭘 할지"만 담는다. 새 세션마다 새 파일을 만들지 않고 이 문서를 직접 수정/삭제/갱신한다.

> 문서 관리(2026-10-30~): `Docs/`는 문서 전용 저장소(https://github.com/FChaGD/Merchant_Design.git, `main`). 기획은 `기획/시스템/`·`기획/콘텐츠/`로 분리, 번호는 공용.

---

## A. UI 아트 에셋 (Ludo AI, 2026-11-23~, 활성)

### A.1 작업 플로우·도구
- 플로우(사용자 지시): **기획 확정 → 생성 → 퀄리티 판단 → 적용 → 인게임 퀄리티 판단**. 유료 생성 전 기획 확정 필수.
- 생성: Ludo AI MCP(`generateWithStyle`에 스타일 기준 시안 E를 레퍼런스로). 결과 URL 7일 만료 - 생성 즉시 내려받아 `Assets/Resources/UI/`(Icons·Frames·Textures)에 저장. **시안 E URL도 2026-11-30경 만료** - 이후엔 `Resources/UI/Icons/Gold.png`를 업로드해 레퍼런스로 쓴다.
- 크레딧: 2026-11-23 약 25.5, 2026-11-24 약 27(전투 바닥 타일 시험), 2026-11-26 87(유닛 애니메이션) 사용, **잔여 99.01**(2026-11-26 기준, Indie, 2026-12-01경 충전). 정확한 값은 `getAccount`로 확인.
- 바닥 타일처럼 이음매 없는 반복 텍스처는 `createTile`(square, texture)을 쓴다. 레퍼런스 이미지를 주면 결이 잘고 옅어지는 경향 - 카툰풍은 레퍼런스 없이 `art_style: Western Cartoon`이 가장 잘 나왔다.
- 적용 구조: 구조 빌더는 건드리지 않고 인스톨러 마지막 단계에서 덮어쓴다 - `HubArtSkinBuilder`(Hub 마커 목록)·`FieldArtSkinBuilder`(Field 버튼 씬 범위 일괄+Field 전용 요소)·`SharedPanelArtSkin`(Hub·Field 공용 패널)·`UIArtSkin`(공용 조작)·`UIArtAssets`(경로·9-slice 테두리 단일 출처)·`UIArtImportPostprocessor`(폴더 기준 임포트 설정).
- **함정**: 새 프레임 PNG를 넣을 때 테두리 표(`UIArtAssets.FrameBorder`)를 고친 코드가 컴파일되기 전에 임포트되면 테두리 0으로 들어가고, 강제 재임포트 시 스프라이트 참조가 끊긴다(2026-11-23 전투 바닥 사례). 순서: 코드 컴파일 → PNG 재임포트 → Hub·Field 인스톨러 재실행.

### A.2 문서·진행
| 문서 | 범위 | 상태 |
|---|---|---|
| [117 기초 아이콘·UI 에셋](../기획/시스템/117-2026-11-23-기초_아이콘_UI_에셋_기획.md) | 재화 HUD 아이콘 4종(골드·마차·용병&시설·평판), 패널·버튼 프레임, Hub 메인·시설 버튼·Field 전 버튼 | 생성·적용·인게임 판단 완료, **커밋 `dab3828`** |
| [118 Field UI 에셋](../기획/시스템/118-2026-11-23-Field_UI_에셋_기획.md) | 큰 패널 프레임, 말풍선·꼬리, 게이지 틀·채움, 알림·경고 띠, 드롭다운 화살표·체크·토글, 정비창 배치 칸, 전투 바닥 타일 | 생성·적용·인게임 판단 완료(토글 체크 상태만 보류), 2026-11-24 화물 회수 UI 수정 포함 메인 커밋 |
| [119 전투 바닥 다종 타일·노이즈 배치](../기획/시스템/119-2026-11-24-전투_바닥_다종_타일_배치_기획.md) | 바닥 재질·비율·노이즈 배치·로딩 계산. §11(2026-11-25): 설계 120 인게임 A4(잡초·꽃 바둑판) 대응 → 스플랫 셰이더 채택 | 확정 |
| [120 전투 바닥 타일맵](../설계/120-2026-11-25-전투_바닥_타일맵_배치_아키텍처.md) | Tilemap 2층 | 제작·인게임 테스트(A4 외 통과) 후 렌더링 부분을 121로 대체 |
| [121 전투 바닥 스플랫 셰이더](../설계/121-2026-11-25-전투_바닥_스플랫_셰이더_아키텍처.md) + 제작계획 | 메시 1장 + HLSL 스플랫 셰이더, 꼭짓점 판정, 육각 샘플링, 띠 PNG 4장·경계 노이즈 | 제작 완료(2026-11-25, EditMode 1057/1057, 인스톨러 3종 실행·저장, CLI 배틀 테스트 캡처 확인), **사용자 인게임 확인(§11) 대기**, 메인 미커밋 |
| [제작 가이드 05 전투 바닥 타일](../제작/05-2026-11-25-전투_바닥_타일_제작_가이드.md) | 향후 바닥 그림 제작 절차(기획→Ludo 생성→로컬 후처리→섞어 깐 미리보기→굽기·적용→조정, 새 재질 추가 범위) | 작성 완료 |
| [122 유닛·전투 표현 에셋](../기획/시스템/122-2026-11-25-유닛_전투_표현_에셋_기획.md) | 118 E 묶음(유닛·마차·시설·투사체·HP바·잔해) | 기획 중 - 대기·이동 시험 생성·카툰 타이밍 판단 완료(§5·§7) |
| [123 전사 스프라이트 애니메이션 시험 적용](../설계/123-2026-11-26-전사_스프라이트_애니메이션_시험적용_아키텍처.md) + 제작계획 | 배틀 테스트 씬 아군 전사, 데이터+코드 재생(A안) | 제작 완료(2026-11-26, 1075/1075, CLI 확인), **사용자 인게임 판단 대기**, 메인 미커밋 |

- 2026-11-24 화물 회수 UI 수정(사용자 지적): 제목 줄 바탕 제거(글자만)·제목/[완료]를 큰 패널 테두리 안쪽으로, 임시 보관 보이는 영역을 테두리 안쪽 14px로 줄임(스크롤 물품이 패널 밖으로 보이던 문제, Hub 인벤토리 팝업·무역품 구매 화면도 같은 조립 코드라 함께 적용). Build Hub/Field 실행+저장 완료, 2026-11-25 사용자 화면 확인 정상.

- 결정 사항: 스타일 = 카툰풍 시안 E(B 형태+C 화풍), 양피지+가죽 테두리 프레임, 버튼 1종 통일, 평판 = Y자 마네킹+FX 후광(C1 몸 하얗게 → W1), 인벤토리 단축 버튼 = HUD 아래 120px·상단 물류품만 30% 축소·소모품과 중앙 정렬, 소지량 툴팁 겹침 허용.
- 보류(기획 118 §7): E 유닛·전투 표현(아이콘 소재·아군/적 구분 방식), G 화물 아이템 그림·H 인물 그림(무역품·캐릭터가 자리표시자 데이터).

### A.3 다음 작업
0. **설계 124·125 사용자 인게임 판단 대기**(2026-11-27 제작 완료) → 검진 여부(124·125 함께). 0.5m 유지·측면 공격 폐기 확정. 상세는 §A.5.
1. **기획 122 잔여 미정 §6**: 부족분(궁수·방패병·괴수·적대자) 생성 방식, 스타일 통일 기준, 동작 목록, 진영 HUD 세부, 전사 2종 배정, 마차·시설·투사체·잔해.
2. **설계 121 인게임 확인**(§11 A1~A10·B1~B2·C1~C2, A4가 개정 목적) → 검진 여부. 미확인: Field 실전투 화면, 유닛 위 그리기, 이전 바닥과 밝기 비교.
3. 보류: 기획 118 토글 체크 상태 - 덮어쓰기 토글이 비활성(설계 12 §6-1)이라 화면 확인 불가, 개체별 방향성 도입 시 확인.
4. 참고(미수정): Field 방향성 지시 패널 위 디버그 정보 줄이 "파티 전체" 탭과 겹침(기존 문제, 디버그 UI 이동 대상).
5. 메인 미커밋 잔여(이 작업과 무관, 2026-11-24 12:16 세션 밖에서 수정됨 - 출처 확인 필요): `EventEncounterCoordinator.cs`·`EventEncounterDirector.cs`·`RestEventSession.cs`·`EventEncounterDirectorTests.cs`. 테이블 `.asset` 7개는 줄바꿈(LF/CRLF)만 다름, 폰트 대체 에셋은 런타임 글리프 갱신.
6. 저장 잔여: 메인 content 커밋 3개(ef8de85·dab3828·c814c70) 미푸시. 미커밋: 설계 123 제작분(`Scripts/Core/Battle/Visual/`·`Scripts/Editor/Art/`·`Shaders/SpriteFlash.shader`·`Assets/Art/Units/`·BattleTest 씬·`BattleCharacterUnitView`·`BattleHealthGaugeView`·`BattleCharacterUnit`·`IBattleCombatant`·BattleTest 진영/규칙·`EditorBattleWorldBuilder`·`BattleTestSceneInstaller`·테스트 5개 파일·공유 프리팹)·`ArtSource/UnitAnim/`(원본·타이밍·tools), `CLAUDE.md`(그래픽 결정 비교 이미지 절)·`ArtSource/`(build_final.py 띠·노이즈 출력 포함)·`예시/`·프롬프트 txt·설계 120+121 제작분(Floor 코드·셰이더·머티리얼·BattleFloorSet·Resources BattleFloor(띠4·노이즈·Grass_1, DirtEdge·낱장 삭제)·씬 3개·테스트·임포트 후처리). 문서 저장소: 기획 118·119·122·설계 120·121(+제작계획)·설계 123(+제작계획)·제작 가이드 05·색인·세션 인계 미커밋.

### A.4 작업 환경 메모
- Unity CLI 캡처: Play 후 `runInBackground`를 Hub 로드 폴링 안에서 같이 켜야 프레임이 멈추지 않는다(앞에서 한 번만 켜면 로드 중 5초 타임아웃으로 실패하는 경우 있음). Field 진입은 `ITripDeparture.TryDepart`+`GameManager.RequestSceneTransition(Field)` eval, 전투는 `EncounterManager.DebugTriggerEncounter()`. 배치 유닛이 없어 전투는 즉시 패배 - 전투 화면은 `capture_game_view --source camera --camera Camera`로 확인.
- 빈 `Assets/Temp/`·`Assets/Temp.meta`: 캡처 임시 폴더, Unity가 잡고 있어 미삭제 - 커밋 제외, 수동 삭제 필요.


### A.5 유닛 애니메이션 (기획 122·설계 123·124, 2026-11-26~)

**확정 방향(사용자)**
- 모습: SD 앞/뒤 두 벌(하·좌하·우하 = 앞, 상·좌상·우상 = 뒤, 좌우 반전 없음). 지금은 앞모습만 생성, 뒷모습은 미생성(게임에서는 앞모습으로 대체).
- 연출: **대기는 부드럽게, 이동(걷기·달리기)은 카툰식으로 딱딱하고 과장되게**. 카툰 = 핵심 포즈만 남기고 중간 프레임 생략, 같은 프레임 재사용으로 머무름·반동, 표시 시간은 일정 박자 배수(사이 프레임 40~50ms는 버벅임으로 보임).
- 진영 구분은 그림 틴트 금지 → HUD(세부 미정). 그림 크기 1m, 판정 크기 불변.

**생성 절차(검증됨)**
1. 입력: `예시/` 원본 → 알파 보정(245↑=255, 12↓=0)·외곽 자르기·여백 8%·640px → `ArtSource/UnitAnim/<날짜>/<캐릭터>-정면-input.png`.
2. Ludo `animateSprite` **Hydra 3초 25장**(9크레딧), loop. 큰 입력 이미지는 MCP 인자로 못 넘김 → `ArtSource/UnitAnim/tools/ludo_call.py animateSprite <args.json>`(args 안 `"@file:<경로>"`를 data URI로 치환, 키는 `~/.claude.json`에서 읽고 출력 안 함) → `tools/fetch_jobs.py <날짜폴더> <클립이름>=<job_id>`로 수신 → `frames/*.webp`를 `frames_png/*.png`로 변환.
3. **얼굴 지시 필수**: 지시문 끝에 `; the face stays a blank dark shadow with no eyes and no facial features`(빠뜨리면 도적 대기에 눈이 생겼다).
4. 확인: 프레임 격자·얼굴 확대·머리 위치(top) 곡선으로 정점/디딤 프레임 찾기 → 타이밍을 `cartoon_timing_채택.txt`에 추가, 표시 이름 `labels.txt`(값 `-`면 숨김) → 에디터 창 `Tools/Game/Art/Unit Anim Preview`(타이밍 파일마다 한 줄, GIF 판단 금지).
5. 도적 단검: Ludo가 날 질감을 매끈하게 다시 그림 → `tools/dagger_texture.py <입력그림> <클립폴더>`로 입력 그림 질감을 옮겨 칠함(원본 `frames_png_raw/` 백업, 재실행 안전).
6. 게임 반영: `ArtSource/UnitAnim/import.txt` 줄 추가(`<직업> <모습> <동작>: <날짜>/<클립> @ <타이밍파일> <loop|once>`) → `Tools/Game/Art/Import Unit Animations`. 크기 기준 = 첫 프레임의 무기 제외 몸(머리 위~발).

**현재 에셋(`ArtSource/UnitAnim/2026-11-26/`, 타이밍 `cartoon_timing_채택.txt`)**
| 표시 | 클립 | 내용 | 게임 반영 |
|---|---|---|---|
| C | `idle_hydra` | 검사 대기(부드럽게, 정점 13번) | O(배틀 테스트 전사) |
| E | `walk_bounce_hydra` | 검사 걷기(디딤 83/정점 250ms) | O |
| I | `run_hydra` | 검사 달리기(디딤 67/정점 133ms, loop 0.79) | X(달리기 사용 조건 미정) |
| F2 | `bandit1_idle3_hydra` | 도적1 대기(단검 보정) | X(적 연결 설계 필요) |
| G | `bandit1_walk_hydra` | 도적1 걷기(단검 보정) | X |
| H | `bandit1_run_hydra` | 도적1 달리기(단검 보정) | X |
- 폐기·숨김: A(Ignis 대기, 입 생김)·B/D(비교용)·F(도적 대기 v1 눈 생김, idle2 단검 미보정). 지난 타이밍 v1~v4는 `timing_archive/`.
- 알려진 차이(미조치, 사용자 판단 대기): 검사 걷기·달리기는 칼을 수평으로 길게 다시 그림(대기는 입력처럼 사선 아래).

**게임 코드 현황(설계 123, 배틀 테스트 씬 아군 전사만)**
- 데이터: `UnitSpriteClip`(이름·프레임·프레임별 초·Loop·EventFrames) → `UnitSpriteAnimationSet`(앞모습 클립 목록) → `UnitSpriteAnimationLibrary`(직업 → 세트). 재생 `SpriteClipPlayer`, 이동 판정 `MovementStateTracker`(0.05m/s, 대기 복귀 0.15초).
- 표시: `BattleCharacterUnitView`의 자식 `ArtBody`(셰이더 `Game/SpriteFlash`, 피격 시 `_Flash`로 흰색 덮기). 세트 없는 유닛(Field 전체·궁수·방패병·적)은 기존 도형.
- 공격·사망 연출 현행: 공격 = 근접 0.5m 찌르기(0.1초)/원거리 투사체, 사망 = 빨간 틴트 + 1.1배 + 2초 페이드, 도주 = 이동하며 페이드. 클립 데이터에 `Loop=false`(once)·`EventFrames`(타격 프레임) 칸은 이미 있음 - 재생 쪽 처리(1회 재생 후 복귀, 사망 마지막 프레임 유지, 타격 프레임 이벤트)는 미구현.

**공격·사망 (2026-11-27, 기획 122 §8 · 설계 124)**
- 기획 확정: 앞모습 공격 1개(하·좌하·우하 공통, B안 측면은 인게임 확인 후 검토), 타격 프레임 도달 시 피해, 예비 동작 중 대상 사라지면 헛손질·쿨타임 소모, 간격은 공격 시작 기준, 0.5m 찌르기 유지(테스트 후 결정), 사망 애니메이션 미생성(현재 프레임 정지 + 빨간 틴트·페이드).
- 생성(36크레딧, 잔여 약 63.01): 검사 A(얼굴·끝 자세 문제) → A2 재생성(자세 미채택) → A 로컬 보정(`tools/face_trail_fix.py`, 얼굴 8~25·궤적 3~7, 7번 미보정) + 복귀 구간(`sword_return_hydra`, 시작 A25·끝 A1) 합성 `sword_attack_loop`. 도적1 B `bandit1_attack_hydra`(5·6번 단검 보정 제외). 채택 타이밍 `2026-11-27/cartoon_timing_채택.txt`(검사 타격 8번 0.333초, 도적 6번 0.083초).
- 설계 124 A안: 시뮬레이션이 공격 클립의 타격 전 시간으로 피해를 늦춤(`OnAttackStarted` 추가), 화면은 공격 클립 재생 후 대기·걷기 복귀, 약탈자(`Marauder` 키) 배틀 테스트 연결, `import.txt` `hit=` 형식.
- 제작 완료(메인 미커밋): EditMode 1084/1084, CLI 가져오기·씬 빌드·Play 확인(타격 시점·번쩍임 일치). 미확인: 사망 정지 화면, 이동 중 공격 전환.
- 인게임 판단(2026-11-27): 0.5m 유지, 측면 공격 폐기(아래/위 두 벌). 추가 기획 122 §8.7 → 설계 125 제작 완료(메인 미커밋, 1090/1090): 공격 중 AI 이동 정지, 공격 중 도주 모션 캔슬 0.25초(타격 났으면 끝까지 재생 후 도주), 이동 = 달리기(import.txt Walk 줄 원본 교체).
- 2026-11-27 배틀 테스트 버그 수정(메인 미커밋): 전투 시작 후 리셋하면 스폰 포인트 예약분 적이 미리보기로 남던 기존 버그 - 리셋 시 예약 변환 항목 제거(`BattleTestSimulationRule.ResetToSetup`), CLI 재현·수정 확인.
- 2026-11-27 124·125 검진 → 권장 #1(판정 타이밍 값 분리 `UnitAttackTiming`)·#2(`hit=` 오류 경고)·#3(화면 테스트 5개)·#7(클립 캐시) 수정, #6(보류 중 헛손질도 끝까지 재생) 유지. 1098/1098, Play 재확인. 메인 미커밋.
- 이후 과제: 뒷모습(위 방향 공격 포함)·방향 전환, 진영 HUD, Field 적용, 궁수·방패병·괴수·적대자.

---

## 0. 콘텐츠 기획 (2026-10-30~, 대기 - 사용자 엑셀 대기)

### 0.1 배경
시스템은 대부분 구현됐지만 콘텐츠는 비어 있다. 테이블·Yarn 값은 전부 Placeholder 또는 테스트용(용병 "직업이름N", 도시 "도시 이름 N", 아이템 `placeholder-*`, 이벤트 E1~E4·QuestTest·Rest_*, Quest.xlsx 헤더만).

### 0.2 문서

| 문서 | 내용 | 상태 |
|---|---|---|
| [110 세계관](../기획/콘텐츠/110-2026-10-30-세계관.md) | 지리·종족·주인공 | 초안(미정 다수) |
| [111 콘텐츠 기획 전제 조건](../기획/콘텐츠/111-2026-11-05-콘텐츠_기획_전제_조건.md) | 시나리오 구조, 상인 갈래 5종, 해금 대상, 기획 플로우, 버티컬 슬라이스 | 확정 |
| [112 핵심 시나리오 러프](../기획/콘텐츠/112-2026-11-05-핵심_시나리오_러프.md) | 테마, 발단~결말 줄거리, 진실(세피시니아·탈각한 존재·크리포힛·추적자·단서), 엔딩 3종 | 초안(1단계) |
| [113 명명 출처](../기획/콘텐츠/113-2026-11-05-명명_출처.md) | 지명·인명·용어의 원형 | 갱신 문서 |
| [114 발단 콘텐츠 배치](../기획/콘텐츠/114-2026-11-05-발단_콘텐츠_배치.md) | 발단 #1~#7 위치별 해금·확정 이벤트 | 확정(2단계) |
| [115 발단 기술 점검](../기획/콘텐츠/115-2026-11-05-발단_기술_점검.md) | 가능/확장/신규 분류, 4단계 전 결정 사항 | 완료(3단계) |
| [116 향후 제작 요소](../기획/콘텐츠/116-2026-11-05-향후_제작_요소.md) | 신규 N1~N8, 확장 E1~E7(전부 후보) | 갱신 문서 |

### 0.3 주요 확정 사항(요약 — 근거는 각 문서)
- 플로우: 1 러프 시나리오 → 2 배치 → 3 기술 점검(표시만) → 4 상세화 → 4.5 가벼운 점검 → (시스템 설계→제작) → 5 적용 → 6 플레이 검증.
- 버티컬 슬라이스: 구간 단위로 2~6단계를 끝까지 통과. 첫 구간 = 발단.
- 엔딩: 멀티(귀향·의식 완성·도주), 모든 엔딩 후 계속 플레이. 막 단위 구분 없음.
- 시작 상태: 모든 기능·UI 잠김, 메인 퀘스트만 예외.
- 4단계 진행 방식: 에이전트가 상세화 항목 제시 → 사용자가 플롯·대사를 엑셀로 작성해 전달 → 그것을 토대로 상세화.

### 0.4 다음 작업
- **다음 세션 시작점: 발단 4단계 상세화.** 아래 항목을 사용자에게 제시함(2026-11-05) → 사용자가 플롯·대사를 엑셀로 작성해 전달(# 단위 분할 가능) → 받은 내용을 토대로 상세화 문서 작성. 서사는 창작 금지, 수치는 범위·의도만.
  - 엑셀 양식은 자유. 제안 구성: 플롯 시트(`#`·`장면`·`내용`), 대사 시트(`장면`·`화자`·`대사`·`선택지·분기`·`비고`). 빈 템플릿 생성 여부는 사용자 답 대기.

| # | 서사(플롯·대사) | 수치(범위·의도) |
|---|---|---|
| 1 | 깨어남 대본(이벤트 대화), 쪽지·목걸이 묘사, 기초 자금 습득 장면, 최초 확정 이벤트 인카운터 내용(만남·선택지·결과), 첫 목적지 마을 | 기초 자금 액수 |
| 2 | 이피네 만남·합류 방식, 상행 중 대화(주인공·이피네), 첫 마을 진입 이벤트 대본, 첫 마을 이름·설명·규모 | — |
| 3 | 사정 청취·귀향 결정 대화, 최초 휴식 중 이벤트 대본, 첫 메인 퀘스트(제목·설명·목표) | 숙소 휴식 비용 |
| 4 | 위치 확인 대화, 주점 정보·소문 문구(직행로 위험 등), 인근 마을 목록(이름·규모·위치 관계) | — |
| 5 | 상단 결성 장면, 아블린다 무역품, 고용 가능 용병(이름·직업·소개), 마구간 품목, 메인 퀘스트 진행 단계 | 무역품 가격·차익, 고용비, 첫 습격 인카운터 적 구성·난이도 의도 |
| 6 | 우회 계획 대화, 게이트 정보 획득 방식, 잡화상점 품목 | 잡화상점 가격 |
| 7 | 출발 당일 습격 대본, 산맥 넘기·이피네 반응, 발단 마무리 장면 | 습격 전투 적 구성·난이도 의도 |
| 공통 | 기능 해금 알림 문구 여부·문구 | — |

- 110 세계관 미정(정령·어인·거인·기타 종족 지향, 식별자 철자, 주 무대 밖 범위, 내부 세력), 113 진조·낙아·수왕 출처 미확인.

### 0.5 커밋·푸시 상태
- 문서 저장소: `74ba2c6`~ 이후 커밋 전부 **미푸시**(2026-11-23 `c5ffa27`·`cdbcd2e`·`ead2661` 포함).
- 메인 `content`: `ef8de85`·`dab3828`(UI 아트 117) **미푸시**. 폰트 에셋(`LiberationSans SDF - Fallback.asset`) 수정분은 세션 시작 전부터 있던 것으로 커밋하지 않음.
- statusline: `.claude/statusline.ps1`에 stdin UTF-8 지정 추가(한글 세션 이름으로 JSON 파싱 실패하던 문제, 2026-11-05). gitignore 대상.

## 1. 현재 상태 요약(2026-09-03 기준 — 데이터 테이블·도시 지도 작업)

`develop`에서 분기(battle 병합 이후). 이 브랜치에서 진행하던 작업 2건 **전부 기획→설계→제작→Unity 실전 검증→커밋→push 완료**:
1. 콘텐츠 데이터(용병/적 스탯, 방향성 지시 옵션)의 엑셀 임포트 파이프라인 전환(§3) — 커밋 `793a515`.
2. 상행 준비 UI 지도의 도시/경로 배치 영구 저장 + 엑셀 테이블화 + Id 전면 정수화(§6) — 커밋 `4e8b03e`.

두 커밋 모두 `origin/content`에 push 완료, 그리고 `develop`에 fast-forward 병합 후 `origin/develop`에도 push 완료(2026-09-03). **이 문서 기준으로 `content` 브랜치의 활성 작업은 없다** — 다음 작업은 `system` 브랜치로 이어간다(그쪽 세션 인계 문서 참고/신설).

## 2. 남은 잡음 (급하지 않음)

1. `Assets/_Recovery/`(Unity 자동복구 씬 백업), `BootstrapDefaultSceneOpener.cs`, `ProjectSceneSettingsInstaller.cs`가 여전히 미커밋 상태로 남아있다 — 데이터 테이블/도시 지도 작업과 무관해 두 커밋 범위에서 의도적으로 제외했다(§5 참고). `_Recovery`는 커밋 대상인지 자체가 불명확 - 사용자에게 필요 여부 확인 필요.
2. §4의 나머지 미정 사항(교차 FK 검증, 상시 검증기 신설 여부, xlsx Git 관리 세부 방침)은 급하지 않음 - 필요 시 별도 기획으로.

## 3. 데이터 테이블 엑셀화 v2 — 제작+Unity 검증+커밋 완료

기획: [Docs/기획/시스템/14-2026-09-02-데이터_테이블_엑셀화_기획.md](../기획/시스템/14-2026-09-02-데이터_테이블_엑셀화_기획.md) §6
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

기획 [14번](../기획/시스템/14-2026-09-02-데이터_테이블_엑셀화_기획.md) §1~§5, 설계 [17번](../설계/17-2026-09-02-데이터_테이블_임포트_아키텍처.md). `EditorTableReader`/`GetOrCreateAsset<T>`/get-or-create 임포터 패턴/Provider 어댑터 구조(`TableBattleUnitStatProvider`/`TableEnemyTypeCompositionProvider`, 인터페이스 무변경) 자체는 v2도 그대로 계승 — 재론 불필요. Placeholder 2종(`PlaceholderBattleUnitStatProvider`/`PlaceholderEnemyTypeCompositionProvider`) 삭제, `ExcelDataReader` 플러그인 배치(`Assets/Plugins/Editor/ExcelDataReader/`)는 v1에서 이미 완료된 채 유지됨.

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

**v2(엑셀 테이블화)**: 기획 [15번 §7~§8](../기획/시스템/15-2026-09-03-도시_지도_데이터_저장_기획.md), 설계 [20번](../설계/20-2026-09-03-도시_지도_데이터_엑셀테이블화_아키텍처.md). v1 방식("저장" 버튼이 SO 에셋에 직접 씀)을 "드래그+엑셀 둘 다 유효, 엑셀이 단일 진실 소스"로 전환. Id 전면 정수화 이후 **Unity 컴파일 성공 확인됨**(2026-09-03). **기능 검증 전부 통과**(사용자 확인, 2026-09-03): 지도 표시/경로 추가/String 테이블/재시작 복원/신규 도시 String 자리표시자 자동 추가. 저장 도중 발생한 `IOException`(엑셀이 파일을 열어둔 상태)은 코드 버그가 아니라 파일 잠금 문제로 진단, `TripCityMapPersistence.Save`에 안내 메시지를 남기는 방어 처리 추가 후 재검증까지 완료. **커밋 완료**(`4e8b03e`), `content`/`develop` 양쪽 push 완료.

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

기획: [Docs/기획/시스템/15-2026-09-03-도시_지도_데이터_저장_기획.md](../기획/시스템/15-2026-09-03-도시_지도_데이터_저장_기획.md)
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
