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
- **v2.7** — (2026-05-27 회의) U열 comorbidity **코드 7(노인/노령) 신설** + **코드 2(심부전)** 선정기준 명시 시만 부여 명문화 + M열 setting 불확실 시 NR(추정 금지) + **Median/Quartile(IQR) 데이터 메타분석 제외**(V열 val2_type에 `IQR` 표식) + 한약 다중처방 처리(전원 동시투여 합산 / 개별화 처방(변증 분기)) + comparison_type **`KM+WM_vs_KM` 제외**(차이가 양방약, PICO 불일치). 열 구조는 v2.6 이후 동일(48열). (현재 버전)

각 버전은 Git 태그로 관리되며, [Releases](https://github.com/trisss1011/af-cpg-extraction-skill-cowork/releases)에서 zip으로 다운로드할 수 있습니다.

## 사용 방법

1. 최신 Release에서 zip 파일을 다운로드하고 압축 해제합니다.
2. `01_매뉴얼/` 안의 매뉴얼 문서를 먼저 읽습니다.
3. `00_skills/` 의 스킬을 Claude 환경에 로드해 사용합니다.

## 라이선스 / 이용

본 저장소는 심방세동 CPG 개발 프로젝트 내부 사용을 목적으로 하는 **Private 저장소**입니다.
