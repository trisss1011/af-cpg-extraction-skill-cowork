# AF CPG Extraction Skill

심방세동(Atrial Fibrillation, AF) 한의표준임상진료지침(CPG) 개발 작업에서 **논문 데이터 추출**을 자동화하는 Claude Skill입니다.

## 개요

본 저장소는 Claude Cowork 환경에서 동작하는 데이터 추출용 스킬과 작업 매뉴얼, 템플릿을 포함합니다. 체계적 문헌고찰(Systematic Review) 과정에서 다수의 RCT 논문으로부터 PICO, 결과지표, 비뚤림 위험 등을 일관된 형식으로 추출하기 위해 사용됩니다.

## 폴더 구조

| 폴더 | 설명 |
|------|------|
| `00_skills/` | Claude 스킬 본체 (cpg-data-extraction, merge-skill, upgrade-skill) |
| `01_매뉴얼/` | 작업자용 사용 매뉴얼 (.docx) |
| `02_papers/` | 추출 대상 논문 예시 |
| `10_참고자료/` | 논문 선정/배제 기록 등 참고 데이터 |
| `90_Output/` | 추출 결과 템플릿 및 예시 산출물 |

## 버전 이력

- **v2.4** — 최초 실배포 버전
- **v2.5** — comorbidity 코드 6 추가 (高栓塞·高出血 위험)
- **v2.5.1** — 토의목록 append 기능 + merge-skill 서식 보존
- **v2.6** — study_design 분류 개정 (随机 표현만으로 RCT 인정) + analysis_set 신규 AU열 (ITT/PP/NR, 48열) + AF with RVR 코드 5 엄격화 (HR≥110 명시 시만) + HRV 파생 지표 아웃컴 완전 제외 + SAE 통합 추출 + 분류 모호 케이스 의무 명시
- **v2.6.1** — (1) **인프라**: upgrade-skill 신설 (단일 트리거 셀프 업그레이드) + `_meta` 시트 도입 (마스터 spec_version 추적). 작업자는 `"업그레이드 해줘"` 한 마디로 본인 마스터 마이그레이션·소급 적용. (2) **추출 규칙 보강** (2026-05-19·05-20 회의): KM vs WM 단독비교 제외(표준치료 add-on 연구만), 3arm은 해당 2arm만 추출. S열 코드 5(AF with RVR) 케이스 세분화. T열 af_type_text 자유 텍스트 규칙 신설. AN/AU 분석집단 보수적 판정 + **FAS 정식 코드 추가** (ITT/PP/FAS/NR 4종). QOL 미네소타 인용 척도 통일 명칭 = `QOL(Minnesota cited)`. 열 구조는 v2.6과 동일 (48열).
- **v2.7** — (2026-05-27 회의) U열 comorbidity **코드 7(노인/노령) 신설** + **코드 2(심부전)** 선정기준 명시 시만 부여 명문화 + M열 setting 불확실 시 NR(추정 금지) + **Median/Quartile(IQR) 데이터 메타분석 제외**(V열 val2_type에 `IQR` 표식) + 한약 다중처방 처리(전원 동시투여 합산 / 개별화 처방(변증 분기)) + comparison_type **`KM+WM_vs_KM` 제외**(차이가 양방약, PICO 불일치). 열 구조는 v2.6 이후 동일(48열).
- **v2.8** — (2026-06-01 회의) QoL **SF-36 통일명 `QOL(SF-36 cited)`**(표준만 MD / 비표준 분리 SMD) + **Study ID 중복 a/b 접미사**(추출 시 임시 부여, merge 시 번호순 재부여) + **다군(3·4·5arm) 가장 적합한 1쌍만**(양약 최다군을 대조) + **양약 종류·용량차 제외**(`KM+WM_vs_WM`라도 양군 양약 다르면 제외; §1.AD-01 보류→확정, 2032 등 소급) + **RCT 표현 충돌 우선순위**(무작위+순서배정 동시 시 순서배정 우선 → non-RCT). upgrade-skill v1.3(v2.7·v2.8 소급 명세), merge-skill v2.7(번호순 a/b 재부여) 동반. 열 구조는 v2.6 이후 동일(48열).
- **v2.8.1** — (버그 수정) 6C 채팅 출력 구조에 **RoB 근거(AJ~AT 11열) 블록 누락 교정** — RoB가 엑셀에만 기록되고 채팅에 출력되지 않던 6B↔6C 불일치 해소(`⑤ RoB 근거` 블록 신설, 기존 불확실·NR→⑥). 매뉴얼 docx 동기화. 추출 규칙·열 구조·코드값 변경 없음(48열, 회의 결정 무관).
- **v2.9** — (2026-06-08 회의) **TER 4종 통일**(`TER`/`TER-Holter`/`TER-ECG`/`TER-TCM`, 전부 Important, 기준 불명 시 단순 `TER`, 한 연구 중복 투입 방지) + **comorbidity 코드 8(냉동소작) 신설**(冷冻球囊 등 비-RF 카테터소작, RFCA 코드 1과 별개). 동반 upgrade-skill v1.4(`v2.9_changes.md`, LATEST 2.9). + **SKILL 문서 경량화**(frontmatter changelog·인라인 버전태그·한약 §3A 중복 정리 — 규칙 불변, 독립 verifier 확인). 열 구조 v2.6 이후 동일(48열).
- **v2.9.1** — (2026-06-15 회의) **배제(exclude=Y) 논문 시트 처리 버그 패치** — 배제 논문은 기본정보 행만 보존하고 아웃컴·한의중재_한약 시트 내용은 자동 삭제(추출 미보존 + merge 제거 + 기존 마스터 소급). 동반 merge-skill v2.7.1·upgrade-skill v1.5(`v2.9.1_changes.md`, LATEST 2.9.1). + **comorbidity 코드 6(고혈전 색전) 과거 평가지표 인정**(과거 CHADS2 등도 발표 당시 기준 점수 충족 시 코드 6 부여) + **중복 논문 2케이스 처리**(완전 동일 삭제 / 번호만 다르면 후순위 exclude=Y, 카운트는 study_id 기준). 열 구조 v2.6 이후 동일(48열). (현재 버전)

각 버전은 Git 태그로 관리되며, [Releases](https://github.com/trisss1011/af-cpg-extraction-skill-cowork/releases)에서 zip으로 다운로드할 수 있습니다.

## 사용 방법

1. 최신 Release에서 zip 파일을 다운로드하고 압축 해제합니다.
2. `01_매뉴얼/` 안의 매뉴얼 문서를 먼저 읽습니다.
3. `00_skills/` 의 스킬을 Claude 환경에 로드해 사용합니다.

## 라이선스 / 이용

본 저장소는 심방세동 CPG 개발 프로젝트 내부 사용을 목적으로 하는 **Private 저장소**입니다.
