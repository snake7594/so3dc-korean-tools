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
