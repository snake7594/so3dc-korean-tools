# 첫 표시 텍스트 정적 식별

## 결론

현재 PCSX2 캡처로 확인되는 기본 기동 순서에서 `mclib`로 그려지는 첫
문자열은 오프닝 크레딧의 **`Game Designer`**이다. 대상은 archive **66**,
stream **57**, message ID **10**이다. 바로 다음 ID 11은
`Masaki Norimoto`이다.

그보다 앞서 캡처된 `S.D. 772`와 `A.D. 1961`은 archive 66의 1,220개
메시지 안에 없다. 실제 32px atlas의 코드 매핑으로 `S.D.`와 `A.D.`의
정확한 bytecode prefix를 만들어 mclib 전체를 검색했지만 일치값은 0개였다.
따라서 두 연대 표시는 이번에 확정한 mclib 메시지 패치 대상이 아니며,
FMV/별도 그래픽 경로로 취급해야 한다.

archive 38의 message ID 1은 번호상 가장 앞선 공용 시스템 문자열이지만,
내용이 프로그레시브 출력 전환 확인창이므로 조건부 화면이다. 현재 기본
기동 캡처에는 나타나지 않았다. 즉 **낮은 message ID와 실제 표시 시각은
같지 않다.**

## 1순위: 기본 기동에서 관측된 첫 mclib 문자열

| 항목 | 값 |
|---|---|
| archive ID | 66 |
| archive LBA / ISO offset | 240706 / `0x1D621000` |
| archive sectors | 70 |
| raw archive | local extraction `output/disc1/raw/0066.slz` (not distributed) |
| raw SHA-256 | `9FF2AACB6EFD2DDD33CFF55C5BAE46B32C06A6B9C2B413C372A70C100F11738C` |
| stream ID / source offset | 57 / 0 |
| SLZ mode | 2 |
| compressed payload / decoded size | 128278 / 346112 |
| decoded resource | local extraction `output/disc1/decoded/0066/s000057_d0_o00000000.mclib` |
| decoded SHA-256 | `77E595E98AA886F74D8F27695D987642895D08C28A8CE3D5F243518D959ECDE4` |
| geometry | 32x32, 4bpp, base 1, 624 slots |
| mapping count | 1220 |

message ID 10의 세부 위치:

- table row: mclib `0x80`
- text blob start: mclib `0x2700`
- message relative offset: 0
- segment length: 14 bytes
- encoded bytes: `01 02 03 04 05 06 04 07 08 09 0A 04 0B 00`
- 렌더 결과: `credits_opening/message_000010.png`

message ID 11:

- table row: `0x88`
- mclib file offset: `0x270E`
- segment length: 16 bytes
- text: `Masaki Norimoto`
- 렌더 결과: `credits_opening/message_000011.png`

ID 12와 13은 같은 첫 크레딧의 일본어 표기인 `ゲームデザイン`,
`則本 真樹`이다. 현재 캡처에서는 영문 ID 10/11이 실제로 표시되었다.

관측 순서의 근거는 다음 캡처다.

1. `work/disc1/title_state/Screenshot.png`: `S.D. 772`
2. `work/gs_font/slot4/Screenshot.png`: `A.D. 1961`
3. `work/gs_font/slot5/Screenshot.png`: `Game Designer` / `MASAKI NORIMOTO`

ID 10/11의 렌더와 세 번째 캡처의 글자 내용 및 서체가 일치한다.

### 첫 한글 출력용 권장 문자열

ID 10의 번역은 **`게임 디자이너`**가 자연스럽다. 이 메시지는 제어 명령이
전혀 없고 원래 14바이트이며, 한글 6종과 기존 공백 한 칸만 있으면 된다.
첫 실행 증명만 목표로 할 경우 고정 길이 안에서 다음처럼 구성할 수 있다.

- 새 glyph 6개: `게`, `임`, `디`, `자`, `이`, `너`
- 기존 공백: code 5
- 7개 코드 + NUL 뒤를 0으로 채워 14바이트 유지

32px atlas의 width table은 `0x6580`, bitmap array는 `0x6800`, glyph당
512바이트다. 새 슬롯을 추가하지 않고 시험하려면 사용 빈도가 낮은 기존
코드 17, 24, 25, 26, 27, 28을 임시로 쓸 수 있다. 전체 1,220개 메시지에서
각 사용 횟수는 1, 4, 15, 7, 9, 34회다. 다만 이 방식은 뒤쪽 크레딧 일부를
손상하므로, 최종 패치에는 atlas 확장/재배치를 사용해야 한다.

## 2순위: 조건부 공용 시스템 첫 메시지

archive 38 stream 19 message ID 1은 완전히 렌더됐다.

| 항목 | 값 |
|---|---|
| archive ID | 38 |
| archive LBA / ISO offset | 238820 / `0x1D272000` |
| archive sectors | 61 |
| stream ID | 19 |
| stream ISO offset | 489103360 (`0x1D272000`) |
| decoded resource | local extraction `output/disc1/decoded/0038/s000019_d0_o00000000.mclib` |
| decoded SHA-256 | `29C6CE3974A17290F4A21361022584F646C17B12FAD1D0A90620ACE14CF8462E` |
| message ID / row | 1 / `0x80` |
| text file offset / length | `0xF80` / 417 bytes |

첫 문장은 `プログレッシブ表示に切り替えますか？`이며, 16:9/4:3 선택과
525p 대응 TV 안내가 이어진다. 결과 이미지는
`system_message_000001.png`이다.

이 decoded resource는 모든 기존 EE RAM 캡처에서 `0x0089DF80`에 원본과
동일한 SHA-256으로 존재한다. 따라서 디스크 파일과 실행 중 공용 시스템
라이브러리의 연결은 확정적이다. 그러나 이 화면은 프로그레시브 출력 전환
경로에서만 나타나므로 기본 기동의 첫 한글 출력 대상으로는 ID 10보다
재현성이 낮다.

message 1에서 관측된 layout opcode `82 80`, `88 80`, `9D 80`은 각각 뒤의
한 바이트만 건너뛰어 렌더했다. 의미를 추측하지 않았고, newline `80 80`과
scale `8A 80 + float32`만 기존 검증 의미를 적용했다. 재현 스크립트는
`render_first_system.py`다.

## 날짜 카드가 archive 66 메시지가 아닌 근거

32px atlas에서 코드값은 glyph index + 1이다. atlas를 직접 읽으면 다음과
같다.

- `S` = code 30 (`1E`)
- `A` = code 94 (`5E`)
- `D` = code 6 (`06`)
- `.` = code 269 (`8D 02`)

따라서 prefix는 `S.D.` = `1E 8D 02 06 8D 02`, `A.D.` =
`5E 8D 02 06 8D 02`가 된다. 두 byte sequence 모두 archive 66 mclib에서
검색 결과 0건이다. 날짜 카드가 보이는 시점보다 뒤에 ID 10/11의 실제
크레딧 캡처가 이어지는 점과 함께, 즉시 패치할 첫 mclib 대상을 ID 10으로
좁힐 수 있다.

## 산출물

- `credits_opening/messages.csv`: ID 10~13의 offset/bytecode
- `credits_opening/message_000010.png`: `Game Designer`
- `credits_opening/message_000011.png`: `Masaki Norimoto`
- `credits_opening/message_000012.png`: `ゲームデザイン`
- `credits_opening/message_000013.png`: `則本 真樹`
- `system_message_000001.png`: 조건부 프로그레시브 안내 전체
- `system_message_000001.hex`: 원본 417바이트
- `system_message_000001_controls.csv`: 보존해야 할 layout/scale command
- `render_first_system.py`: message ID 1 재현 렌더러
