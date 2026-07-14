# v0.4.0-alpha.1 — 하이다 초반 대사 653개 한국어화

게임 초반 하이다 구간의 24개 이벤트 뱅크에서 확인한 물리적 대사 위치 653개를
한국어로 교체하는 알파 패치입니다. 같은 원문 세그먼트가 여러 위치에서 재사용되는
경우를 하나로 묶어 한국어 번역 레코드 434개로 관리합니다. 게임 전체 대사 패치가
아니며, 이번 릴리스에서 구조와 위치를 확인한 범위만 대상으로 합니다.

## 주요 변경

- 하이다 초반 이벤트 뱅크 `24`개, 물리적 대사 위치 `653`개 패치
- 동일 원문 바이트를 묶은 한국어 화자·본문 레코드 `434`개 제공
- `NanumSquareNeo-cBd.ttf`를 사용해 장면별 24×24 로컬 글리프 생성
- 비목표 메시지가 참조하는 로컬 글리프를 보호하고 목표 전용·미사용 슬롯을 우선
  재사용
- 검증된 전역 숫자·영문·문장부호 글리프를 재사용해 로컬 폰트 증가 억제
- 원문 줄 수와 이벤트·레이아웃 제어 서명을 보존하는 데이터 기반 패처 추가
- 패처 모듈과 분리된 PK1·메시지·글리프 검증 경로 추가
- 원문 텍스트와 추출 게임 바이트가 없는 공개용 compact 구조 매니페스트 제공
- 한국어 데이터만 담은 `translations/hyda_ko.json` 제공

## 지원 및 해시

- 지원 원본: 일본판 Director's Cut Disc 1 (`SLPM-65438`)
- 원본 ISO 크기: `4,689,854,464` bytes
- 원본 ISO SHA-256:
  `95CC4E25AC71DE7C6263AA2E544910DE30667EA3BA62726CF4A019F24B038826`
- NanumSquareNeo-cBd.ttf SHA-256:
  `4749FA5691157CF56A59D297B45E88894A646846048018CD7A4117FFB2869767`
- 출력 ISO SHA-256:
  `7080D2226400C5B3747C7FE92F939E9DAD1EFAE580A81CAA2D371D885238E464`
- xdelta SHA-256:
  `2727B993D6474B3EE696F752CD26B7EE4E169683D85C779CD0A9842648361A2E`
- 릴리스 ZIP SHA-256:
  `3C0377AA441AA10A857B7AAAA4514973A3ED7555FD222565C0BCA9053EDE2EF7`

릴리스 번들 이름:

- `SO3_DC_Disc1_Korean_Hyda_653_Nanum_v0.4.0-alpha.1.zip`
- `SO3_DC_Disc1_Korean_Hyda_653_Nanum_v0.4.0-alpha.1.xdelta`

xdelta는 3.0.11로 다시 적용했으며, 복원 ISO의 크기와 SHA-256이 직접 빌드
ISO와 정확히 일치합니다.

## 정적 검증 범위

- 공개 번역 434개가 매니페스트의 653개 위치를 빠짐없이 한 번씩 덮는지 검사
- 원본 ISO 크기·SHA-256, 폰트 SHA-256과 대상 세그먼트 SHA-256 고정
- 숨겨진 6,144-entry 인덱스의 원시 바이트와 디코딩 결과 불변 검사
- 허용한 24개 아카이브 밖 ISO 변경 `0` bytes 검사
- 대상 653개의 한국어 화자·본문과 이벤트·레이아웃 제어 서명 검사
- 비목표 메시지 379개와 그 메시지가 참조하는 기존 로컬 글리프 불변 검사
- 비목표 PK1 레코드, 이후 PK1 패키지와 꼬리 불변 검사
- 각 장면의 24×24 로컬 폰트를 NanumSquare Neo 기준으로 다시 렌더링해 검사

`v0.4.0-alpha.1`의 에뮬레이터 런타임 검증은 의도적으로 수행하지 않았습니다.
정적 검증은 실제 글자 표시, 화면 폭, 대사 문맥과 게임 진행 확인을 대신하지
않습니다. ISO, 게임 실행 파일, 추출 게임 데이터, BIOS, 세이브스테이트와 TTF
폰트 파일은 배포하지 않습니다.

자세한 구조와 릴리스 검증 기록은
[`docs/HYDA_653_KOREAN_PATCH.md`](docs/HYDA_653_KOREAN_PATCH.md)와
[`docs/releases/v0.4.0-alpha.1-verification.md`](docs/releases/v0.4.0-alpha.1-verification.md)를
참고하세요.

---

# v0.3.0-alpha.1 — NanumSquare Neo 및 초반 한자 독음

첫 대사 한글 출력 패치를 NanumSquare Neo Bold로 다시 빌드하고, 초반 호텔
장면의 일본어 한자 13자를 한국어 독음 bitmap으로 교체하는 알파 패치입니다.
문장 bytecode는 건드리지 않으며 같은 local glyph를 쓰는 위치에 동일한 독음이
표시됩니다.

## 주요 변경

- 첫 대사 한글 글리프를 `NanumSquareNeo-cBd.ttf`로 렌더링
- 24×24 셀, 22px, 3단계 명암 사용
- 전역 glyph count `292 → 306`
- 초반 장면의 `号室無何当一切人工生物使用`을
  `호실무하당일절인공생물사용` bitmap으로 교체
- local glyph 13개의 24×24 bitmap만 바꾸고 width `24` 유지
- 목표 메시지 bytecode, width table, PK1 table, 비목표 record 보존
- 독립 검증기와 합성 테스트 추가

대표 확인 문구:

```text
당ホテルのプライベートビーチには
일절の인공생물が사용されておりません。
```

## 지원 및 해시

- 원본 ISO SHA-256:
  `95CC4E25AC71DE7C6263AA2E544910DE30667EA3BA62726CF4A019F24B038826`
- 출력 ISO SHA-256:
  `FD298A889002FF3AC23B43CF8433B1BE50EECC15ACE81E61DD48B759DF800F9B`
- 릴리스 ZIP SHA-256: `EE9DB79B1330A05882A35151D3A95458BDC3BC20FA6DFB8C9F29F276731DB0FE`
- xdelta SHA-256: `A35D924CA7273DDA676D20AAB74815E3A3BB64DDF71CA212B7534780114DA5A3`
- NanumSquareNeo-cBd.ttf SHA-256:
  `4749FA5691157CF56A59D297B45E88894A646846048018CD7A4117FFB2869767`

릴리스 ZIP과 그 안의 패치 파일:

- `SO3_DC_Disc1_Korean_Kanji_Readings_Nanum_v0.3.0-alpha.1.zip`
- `SO3_DC_Disc1_Korean_Kanji_Readings_Nanum_v0.3.0-alpha.1.xdelta`

## 정적 검증

- v0.3 전용 테스트 `13`개 및 전체 테스트 `28`개 통과
- 전역 폰트 compressed payload `24,496 → 25,383` bytes
- 전역 폰트 allocation `25,440` bytes, 잔여 `57` bytes
- 목표 mclib compressed payload `10,429 → 9,859` bytes
- record allocation `10,448` bytes 유지, 미사용 `572` bytes
- 목표 bitmap 13개 외 local bitmap과 모든 width 불변
- 첫 PK1 table, 비목표 record 11개, 두 번째 PK1 package 불변
- 4.69GB 전체 diff `1,015,191` bytes, 허용 archive 밖 변경 `0` bytes

`v0.3.0-alpha.1`의 에뮬레이터 런타임 검증은 수행하지 않았으며 사용자가 직접
확인합니다. 완성 한글패치가 아니라 폰트와 한자 독음 출력 경로를 검증하기 위한
알파 빌드입니다. ISO, BIOS, 실행 파일, 추출 리소스와 폰트 파일은 배포하지
않습니다.

---

# v0.2.0-alpha.1 — 첫 대사 한글 출력

게임 시작 후 소피아의 첫 대사 메시지 ID 5를 한국어로 교체하는 알파 패치입니다.
원문 메시지의 말하는 사람, 줄바꿈, 대기·타이밍·스타일 제어 순서를 보존했습니다.

## 주요 변경

- archive 8의 전역 24×24 폰트에 한글 14자 추가
- OFL의 Noto Sans CJK KR Regular 사용, 22px·4단계 명암
- 후속 DTT stream을 같은 archive 안에서 이동하고 ZLS 링크 복구
- archive 1220의 장면 폰트 local base `301 → 307`
- 기존 local glyph operand 108개 전부 `+6` remap, bitmap 72개 불변
- 두 이름 호출을 한국어 literal glyph로 치환하고 메시지 제어열 보존
- 공개 패처, 독립 검증기, 합성 테스트, 정적 미리보기 추가

## 지원 및 해시

- 원본 ISO SHA-256:
  `95CC4E25AC71DE7C6263AA2E544910DE30667EA3BA62726CF4A019F24B038826`
- 출력 ISO SHA-256:
  `BBC366A55DA0C2C985BB7E5329A7D2FE8674913995EEB4C81E9674C1E42AF27C`
- 릴리스 ZIP SHA-256:
  `1B7C7E48F440D7F2B82CD65D0E31F025ED6F472FBBE83DE69922629875237893`
- xdelta SHA-256:
  `D0F053E5972D7F1E4C045D706DE028C00513DC814641A9A093BFAB3571C2788B`
- Noto Sans CJK KR Regular SHA-256:
  `6BCB2A0703AA137E874FC2DFFA85F6C21BA9A67FA329E81B8C801663AF7E992A`

## 검증

- 4.69GB 전체 diff: 허용 archive 밖 변경 `0` bytes
- archive 1220 첫 PK1 records: `12` 검사, `11` 불변, `1` 목표 변경
- 전체 추출 manifest root SLZ streams: `63` 검사, `62` 불변, `1` 목표 변경
- xdelta encode/decode 왕복 후 출력 ISO 해시 일치

완성 한글패치가 아니라 첫 대사를 대상으로 한 구조·출력 검증판입니다. 게임 파일,
BIOS, 실행 파일, 추출 리소스, 폰트 파일은 배포하지 않습니다.
