[공지] AF CPG 데이터추출 스킬 v2.6.1 — 셀프 업그레이드 안내
─────────────────────────────────────────────────────────────

v2.6.1에서 새 스킬 `upgrade-skill`이 추가되어, 작업자가 본인 마스터를
Cowork에서 직접 최신 사양으로 업그레이드할 수 있게 되었습니다.
이전 v2.6 공지(마스터 제출 → 심상송 처리 → 회신)는 무효화하고,
아래 셀프 처리 안내로 대체합니다.

【소요 시간】 5분 (자동 처리) + 원문 대조 점검 (사람 확인 항목, 분량에 따라 가변)
【핵심 변경】 v2.6.1 — 인프라 + 추출 규칙 보강
  [인프라]
  - `upgrade-skill` 신설: 단일 트리거로 마스터 자동 업그레이드
  - `_meta` 시트 도입: 마스터의 spec_version 추적 (작업자는 보지 않음, 자동 관리)
  - cpg-data-extraction·merge-skill 본체 알고리즘은 변경 없음

  [추출 규칙 보강 — 2026-05-19·05-20 회의 결정]
  - 논문 포함·제외: KM vs WM 단독비교 제외 (표준치료 add-on 연구만)
  - 3arm 처리: 해당 2arm 비교쌍만 추출 (KM·WM·KM+WM이면 WM vs KM+WM만)
  - S열 코드 5(AF with RVR) 케이스 세분화: (RVR 명시 AND HR≥110) OR (RVR 미언급 AND HR≥110 명시)만 코드 5
  - T열 `af_type_text` 자유 텍스트 규칙 신설: paroxysmal/persistent/RVR/permanent 등 원문 표현 보존
  - AN/AU 보수적 판정: method에 명시된 것만 인정, 결과 표 분모로 추정 금지
  - **FAS 정식 코드 추가**: AU `analysis_set`이 ITT/PP/FAS/NR 4종으로 확장
  - QoL 미네소타 통일 명칭 = `QOL(Minnesota cited)` (No.619·635 등)

  ※ 열 구조는 v2.6 그대로(48열). v2.6.1은 의미·기재 규칙 보강만.

【핵심 변경】 v2.6 (참고 — v2.5.x 마스터를 가지고 있다면 이 단계도 자동 통과)
  - 기본정보 시트 AU열 `analysis_set` 신규 (47→48열)
  - 기존 notes는 AU → AV로 이동
  - study_design 분류 기준 개정 (随机 표현만 있어도 RCT)
  - af_type_other 코드 5 엄격화 (HR≥110 명시 시만)
  - HRV 파생 지표 아웃컴 완전 제외
  - SAE 통합 추출 (AE total과 별개)
  자세한 내용: 01_매뉴얼/AF_CPG_데이터추출_매뉴얼_v2.6.1.docx 참조

────────────────────────────────────────────
[A] 신본 다운로드 + 환경 구성
────────────────────────────────────────────

【1단계】 v2.6.1 zip 수령
  심상송이 보낸 `af-cpg-extraction-skill-cowork-2.6.1.zip`을 바탕화면에 압축 해제
  → `af-cpg-extraction-skill-cowork-2.6.1/` 폴더 생성됨

【2단계】 옛 폴더에서 본인 데이터 복사
  옛 폴더(`af-cpg-extraction-skill-cowork-2.5.1`)에서 새 폴더로 다음만 복사:
    - 90_Output/AF_CPG_data_extraction_<본인이름>.xlsx  → 새 폴더 90_Output/ 에 덮어쓰기
    - 90_Output/extracts/AF_extract_*.xlsx              → 새 폴더 90_Output/extracts/ 로 (있다면)
    - 토의목록_<본인이름>.md                            → 새 폴더 루트로 (있다면)
  ※ skills/, sample/, 매뉴얼은 옮기지 마세요. 신본 사용.

【3단계】 Claude Project 재연결
  ① Claude Desktop Cowork
     - 기존 Project의 폴더 연결을 옛 폴더 → 새 폴더로 변경 (또는 새 Project 생성)
  ② Project Instructions 갱신 (작업자 본인이 직접 손봄)
     - `sample_v2.5.1.xlsx`/`sample_v2.6.xlsx` 문구를 `sample_v2.6.1.xlsx`로 교체
     - 스킬 목록에 `upgrade-skill: 마스터 엑셀을 최신 스킬 사양으로 업그레이드` 한 줄 추가
     - 작업 규칙 첫 항목으로 다음 줄 신설 (v2.6.1 신규):
       `새 task가 시작되면 가장 먼저 skills/ 폴더의 모든 스킬을 인식해
        이름·버전·갱신일을 표로 출력한 뒤 작업 지시를 기다린다.`
     - 트리거에 `"업그레이드", "업그레이드 해줘", "마스터 업그레이드" 등으로 마스터 업그레이드 시작` 한 줄 추가
     ※ 정본 Project Instructions 블록은 매뉴얼 §3-2 참조 (v2.6.1.docx에 트래킹 변경 반영됨)
  ③ 새 task 열기 — 자동 버전 표시 확인
     → task 첫 응답에 다음과 같이 자동 출력되어야 정상:
       인식된 스킬:
       - cpg-data-extraction v2.6.1 (2026-05-20)
       - merge-skill v2.6 (2026-05-19)
       - upgrade-skill v1.2 (2026-05-20)
     → 자동 출력이 안 되면 Project Instructions의 새 줄이 빠진 것 — ② 다시 확인

────────────────────────────────────────────
[B] 본인 마스터 업그레이드 (셀프 처리)
────────────────────────────────────────────

【4단계】 채팅에 한 마디 입력
  > 업그레이드 해줘

  또는

  > 마스터 업그레이드

  Claude가 자동으로 다음을 수행합니다:
    ① 마스터 자동 식별 + 락 파일 확인
    ② `_meta` 시트에서 현재 버전 감지 (없으면 헤더 구성으로 추정)
    ③ 자동 백업 → 90_Output/backups/master_before_upgrade_<시각>.xlsx
    ④ 적용할 변경 미리보기 출력
    ⑤ 작업자 "예" 승인 후 자동 처리
       - 47→48열 마이그레이션 (이미 48열이면 skip)
       - AU analysis_set 코드화 (AN 텍스트 분석)
       - HRV 행 삭제 + AI열 정리
    ⑥ 사람 확인 필요 항목 검출 (자동 변경 X)
    ⑦ 리포트 생성 → 90_Output/retrofit_report_<시각>.md

【5단계】 미리보기 확인 + "예" 입력
  채팅에 다음 형식 미리보기가 나옵니다 (예시):

    업그레이드 미리보기
    ─────────────────────────────────
    현재 버전: 2.5.x  →  목표 버전: 2.6.1
    (v2.6 → v2.6.1 단계 자동 포함)

    [자동 처리 예정]
      [v2.6]
        구조 마이그레이션: 47열 → 48열
        AU analysis_set 채움: N건 (분포: ITT=x, PP=y, NR=z)
        HRV 행 삭제: N건
        AI열 HRV 토큰 제거: N건
      [v2.6.1]
        _meta 키 마이그레이션: schema_version → spec_version (1건)
        spec_version 갱신: 2.6 → 2.6.1

    [⚠️ 사람 확인 필요 — 자동 변경 안 함]
      [v2.6]
        study_design 비표준 값: N건
        study_design 재분류 후보: N건
        af_type_other 코드 5 부적격 후보: N건
        SAE 추가 후보: N건
      [v2.6.1]
        AU analysis_set 추정값 검출(보수적 위반): N건
        FAS 명시 논문 재분류 후보(ITT→FAS): N건
        KM vs WM 단독비교 행 제거 후보: N건
        S열 코드 5 케이스 세분화 재검토: N건
        T열 af_type_text 누락 후보: N건
        미네소타 QOL 변환 후보(MLHFQ→QOL(Minnesota cited)): N건
        3arm 비교쌍 점검: N건

    백업: 90_Output/backups/master_before_upgrade_<시각>.xlsx
    리포트: 90_Output/retrofit_report_<시각>.md

    진행할까요? (예/아니오)

  → "예" 입력하면 적용. 다른 답은 모두 중단(백업은 유지).

【6단계】 ⚠️ 사람 확인 항목 — 채팅창 인터랙티브 처치 (v2.6.1)
  자동 처리가 끝나면 Claude가 다음 형식으로 카테고리별 항목을 채팅에 띄운다.
  작업자는 추출 작업 때처럼 채팅에서 직접 응답하면 스킬이 마스터를 즉시 수정한다.

  사람 확인 항목 — 인터랙티브 처치
  ─────────────────────────────────
  총 N건. 어디부터 처리할까요?
  - "순서대로"  : A부터 L까지 차례로
  - "F부터"     : 특정 카테고리부터
  - "H만"       : 한 카테고리만
  - "지금은 안 함" : 8단계 종료, 리포트 .md만 저장

  → 카테고리 선택 후 항목 목록이 번호 매겨 출력됨. 각 항목에 대해:
     - "전부 제안대로 적용" : 그 카테고리 전체 일괄 처치
     - "1,2 적용, 3 건너뛰기" : 개별 선택
     - "1번 원문 보기"     : 해당 행의 AN/AD/AI 원문 발췌 추가 표시
     - "이 카테고리 건너뛰기" : 통째로 미처리
     - "마침"              : 처리 중단, 남은 항목은 리포트로 보존

  처치한 결과(적용/건너뜀/미처리)는 모두 `90_Output/retrofit_report_<시각>.md`에도
  병행 저장되어 세션이 끊겨도 추적·재개 가능하다.

  ─── 카테고리 (Claude가 표시하는 식별자) ───

  ─── v2.6 항목 ───

  A. study_design 비표준 값
     - `Multi-centre RCT (...)`, `RCT (prospective)` 등 → 검토 후 `RCT`로 변경
     - `Systematic review / Meta-analysis` → exclude=Y 검토

  B. quasi-RCT 재분류 후보
     - 무작위 표현만 있고 비무작위 키워드 없음 → `RCT`로 변경
     - 입원순서·번호순 등 비무작위 키워드 있음 → `non-RCT`로 변경

  C. af_type_other 코드 5 부적격 후보
     - HR 임계값 110 명시 안 됨 → 원문 대조 후 코드 5 해제 (S열 비우기)

  D. SAE 추가 후보
     - SAE 구성 사건(사망/뇌졸중/AF 재발 등)이 2개 이상 별개 행으로 추출된 study_id
     - 원문에 SAE/严重不良反应가 묶어 보고되어 있는지 확인 후, 있으면 SAE 행 수기 추가

  E. HRV 모호 매치 (효과크기 보유 행)
     - 자동 삭제 보류된 행 — 검토 후 수기 삭제 여부 결정

  ─── v2.6.1 항목 (이번 차수 추가) ───

  F. AU `analysis_set` 추정값 검출 (보수적 판정 위반 후보)
     - method에 ITT/PP/FAS 명시 없이 AU가 ITT/PP인 행
     - 원문 대조 후 AU를 `NR`로 변경하거나, method에 실제 명시가 있다면 AN 원문 보강

  G. FAS 명시 논문 재분류 후보 (`ITT` → `FAS` 승격)
     - AN에 `FAS`/`full analysis set` 명시되었으나 AU가 `ITT`로 통합된 행
     - 원문 method에서 FAS 정의가 실제로 적용되었는지 확인 후 AU를 `FAS`로 변경

  H. KM vs WM 단독비교 행 제거 후보
     - Z열 `comparison_type` == `KM_alone_vs_WM`인 행
     - 단순 2arm KM vs WM → `exclude=Y` + `exclude_reason`에 `"v2.6.1: KM vs WM 단독비교 제외"`
     - 3arm 중 KM·WM·KM+WM이면 `KM vs WM` 행만 삭제, `WM vs KM+WM`은 보존

  I. S열 코드 5 케이스 세분화 재검토
     - RVR 단어만 있고 HR 110 미달/미언급 후보 → S열 비우기 + T열에 원문 표현 보강

  J. T열 `af_type_text` 누락 후보
     - T열이 공란/`NR`인데 원문에 paroxysmal/persistent/RVR 등 표현이 있는 경우 → 보강

  K. 미네소타 QOL 변환 후보 (`MLHFQ` → `QOL(Minnesota cited)`)
     - 검출 범위: outcome_std=`MLHFQ` 또는 `Minnesota Living with Heart Failure Questionnaire`인 행만 (다른 지표는 본 룰 대상 X)
     - 검출 트리거: 점수 방향이 표준 MLHFQ(낮을수록 양호)와 자동 판별 룰(R1 원시데이터 / R2 효과크기 부호 / R3 변화량 부호) 중 하나라도 모순되는 행
     - 작업자 처치: 원문 대조 후 (a) 진짜 척도 모순 → outcome_std를 `QOL(Minnesota cited)`로 변경 + notes 보강, (b) 추출 단계 direction 오기재 → direction만 정정
     - 알려진 사례(No.619·635 등)는 자동 검출에 자연히 포함됨 — study_id 우선순위는 별도 두지 않음

  L. 3arm 비교쌍 점검
     - 같은 study_id에 비교쌍 2개 이상 추출된 케이스
     - KM·WM·KM+WM 3군 구성이면 `WM vs KM+WM`만 유지, `KM vs WM`은 제거

【검증 (선택)】
  엑셀로 마스터 열기 → 기본정보 시트 마지막 3열 확인:
    AT=rob_other_coi / AU=analysis_set / AV=notes
  기존 메모(notes)가 AV에 그대로 옮겨졌는지 1~2개 무작위 확인

【7단계】 옛 폴더 보존
  옛 `af-cpg-extraction-skill-cowork-2.5.1` 폴더는 한 달간 그대로 보관.
  안정성 확인 후 archive/로 이동 또는 삭제.

────────────────────────────────────────────
[C] 향후 새 버전(v2.7 등) 출시 시
────────────────────────────────────────────

심상송이 새 zip을 전달하면:
  1. 새 zip 압축 해제
  2. 본인 마스터를 새 폴더 90_Output/로 복사
  3. Claude에 "업그레이드 해줘" 입력

→ 그게 전부입니다. 새 버전이 v2.7, v2.8로 가도 트리거는 항상 동일.
   upgrade-skill이 마스터의 `_meta`를 보고 어디서부터 어디까지 적용할지 자동 판단.

【문의】
  - 업그레이드 실행 중 오류 발생 → 즉시 심상송에게 연락
  - 매뉴얼 v2.6.1의 6장 FAQ가 대부분의 질문에 답합니다
