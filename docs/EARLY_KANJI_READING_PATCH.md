# 초반 한자 한국어 독음 패치 분석

## 범위

`v0.3.0-alpha.1`은 `v0.2.0-alpha.1`의 첫 대사 한글 출력을 유지하면서 다음 두
가지를 검증합니다.

1. 전역 한글 글리프를 NanumSquare Neo Bold로 렌더링해 고정 archive 공간에
   넣을 수 있는지 확인합니다.
2. archive 1220의 초반 호텔 장면에서 일본어 한자 local glyph bitmap을 한국어
   독음 bitmap으로 바꿉니다.

문장 전체 번역 패치가 아닙니다. kana, 문장부호, 제어열과 메시지 bytecode는 그대로
두고 선택한 한자 bitmap만 바꿉니다.

## 한자와 독음 매핑

독음은 Unicode Unihan `kHangul`을 기준으로 초반 장면에 명시적으로 매핑했습니다.
Japanese shinjitai인 `当`은 대응하는 한국 한자음 `당`을 사용합니다.

| 한자 | 독음 | local index | 원래 code | 첫 대사 remap 후 code | width |
|---|---|---:|---:|---:|---:|
| 号 | 호 | 8 | 309 | 315 | 24 |
| 室 | 실 | 9 | 310 | 316 | 24 |
| 無 | 무 | 12 | 313 | 319 | 24 |
| 何 | 하 | 33 | 334 | 340 | 24 |
| 当 | 당 | 39 | 340 | 346 | 24 |
| 一 | 일 | 40 | 341 | 347 | 24 |
| 切 | 절 | 41 | 342 | 348 | 24 |
| 人 | 인 | 42 | 343 | 349 | 24 |
| 工 | 공 | 43 | 344 | 350 | 24 |
| 生 | 생 | 44 | 345 | 351 | 24 |
| 物 | 물 | 45 | 346 | 352 | 24 |
| 使 | 사 | 46 | 347 | 353 | 24 |
| 用 | 용 | 47 | 348 | 354 | 24 |

대표 확인 문구는 다음처럼 표시될 예정입니다.

```text
当ホテルのプライベートビーチには
一切の人工生物が使用されておりません。

↓

당ホテルのプライベートビーチには
일절の인공생물が사용されておりません。
```

같은 local glyph를 공유하는 이 장면의 다른 메시지에서도 해당 한자가 같은 한국어
독음으로 보입니다.

## NanumSquare Neo 전역 폰트

전역 폰트는 archive 8의 24×24 4bpp `so3mclib`입니다. 첫 대사에 필요한 한글
14자를 NanumSquare Neo Bold 22px로 렌더링하고 3단계 명암으로 양자화했습니다.
4단계 명암 빌드는 고정 payload allocation을 60바이트 초과하므로 사용하지
않았습니다.

- 폰트 파일: `NanumSquareNeo-cBd.ttf`
- 폰트 SHA-256:
  `4749FA5691157CF56A59D297B45E88894A646846048018CD7A4117FFB2869767`
- 전역 glyph count: `292 → 306`
- decoded mclib: `84,608 → 88,704` bytes
- compressed payload: `24,496 → 25,383` bytes
- 고정 allocation: `25,440` bytes
- 잔여 공간: `57` bytes
- 명암 단계: `3`
- global archive SHA-256:
  `0C9BB9000FE13B7A0BD5D08057B76BDED341ED9DB0C276BC90C8A1949327692B`
- global mclib SHA-256:
  `843E25AAB01EDECF08FFE267BD9DD85F4816416E67C716B292784E0119F3895D`

NanumSquare Neo는 NAVER와 Sandoll이 제공하며 SIL Open Font License 1.1을
따릅니다. 입력 TTF 파일 자체는 저장소와 릴리스에 포함하지 않습니다.

## archive 1220 bitmap 교체

첫 대사 패치가 local base를 `301 → 307`로 옮긴 중간 산출물을 기준으로 작업합니다.
13개 슬롯 각각의 24×24 bitmap 288바이트를 NanumSquare Neo 독음 글리프로
교체합니다. width table의 값 `24`와 메시지 영역은 바꾸지 않습니다.

- 목표 mclib decoded size: `22,528` bytes, 불변
- compressed payload: `10,429 → 9,859` bytes
- record allocation: `10,448` bytes, 불변
- 실제 사용 record size: `9,876` bytes
- record 내부 미사용 공간: `572` bytes
- 두 PK1 package 사이 gap: `360` bytes, 불변
- target archive SHA-256:
  `58ACAC705237C5FF23C36B6D646BE55C0D71632CDBB60E0443F618D6847659EE`
- target mclib SHA-256:
  `37A167F230D9CE019A9846FC2C4468C1488132D365070EFA361945561B7F4718`

bitmap 교체 전의 첫 대사 중간 산출물과 비교하면 첫 PK1 table, 비목표 상위 record
11개와 두 번째 PK1 package는 그대로입니다. 압축 결과가 기존 allocation보다
작으므로 이번 단계에서는 record 재배치가 추가로 발생하지 않습니다.

## ISO 전수 비교

- 원본 ISO SHA-256:
  `95CC4E25AC71DE7C6263AA2E544910DE30667EA3BA62726CF4A019F24B038826`
- 출력 ISO SHA-256:
  `FD298A889002FF3AC23B43CF8433B1BE50EECC15ACE81E61DD48B759DF800F9B`
- 검사한 크기: `4,689,854,464` bytes
- 전체 changed bytes: `1,015,191`
- archive 8 changed bytes: `25,990`
- archive 1220 changed bytes: `989,201`
- 두 허용 archive 밖 changed bytes: `0`

archive 1220의 큰 diff에는 첫 대사 패치에서 발생한 record 이동에 따른 위치 차이가
포함됩니다. 내용 단위 검증에서는 목표 이외의 상위 record 11개와 두 번째 package가
같음을 확인했습니다. SLZ 압축 해제·재압축 왕복도 통과했습니다.

## 검증 상태

- v0.3 전용 합성·회귀 테스트: `13`개 통과
- 전체 공개 테스트 모음: `28`개 통과
- 숨겨진 archive index: 불변
- 전역 폰트의 다른 root stream 3개: 불변
- 목표 외 local glyph bitmap: 불변
- local width table: 불변
- bitmap 교체 단계의 message bytecode: 불변
- PK1 비목표 record 11개: 불변
- 두 번째 PK1 package: 불변
- 허용 ISO 범위 밖 변경: `0` bytes

정적 구조와 해시는 검증했습니다. `v0.3.0-alpha.1`을 에뮬레이터에서 실행하는
런타임 검증은 수행하지 않았으며 사용자가 직접 확인합니다.
