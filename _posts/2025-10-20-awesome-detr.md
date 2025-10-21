---
layout: post
title: "Awesome Detection Transformer"
date: 2025-10-20 10:34:00 +0900
excerpt: "DETR 계열 검출기 모델 정리"
tags: [object-detection, detr, survey]
---

## DETR 계열 검출기 정리
DETR 계열 검출기 모델들을 정리, Object365 등 추가 데이터를 활용하여 Pre-training한 모델은 제외하고 Backbone으로 ResNet50(R50)을 사용한 모델들 위주로 우선 리스트업.

### 성능표 (MS-COCO val2017)

| Model        | Epochs | AP | Params(M) | Date | Publication |
|--------------|:------:|:-------:|:---------:| :---------:| :------:|
| DETR-DC5 R50     |  500   |  43.3   |   41    | 2020.08 | [ECCV](https://www.ecva.net/papers/eccv_2020/papers_ECCV/papers/123460205.pdf) |
| Deformable-DETR++ (two-stage, R50)   |   50   |  46.2   |   40    | 2021.05 | [ICLR](https://openreview.net/pdf?id=gZ9hCDWe6ke)|
| Conditional-DETR DC5 R50    |  108   |  45.1   |   44    | 2021.10 | [ICCV](https://openaccess.thecvf.com/content/ICCV2021/papers/Meng_Conditional_DETR_for_Fast_Training_Convergence_ICCV_2021_paper.pdf) |
| DAB-DETR DC5 R50    |   50   | 45.7   |   44    | 2022.04 | [ICLR](https://openreview.net/pdf?id=oMI9PjOb9Jl) |
| DN-DETR DC5 R50    |   50   |  46.3   |   44    | 2022.06 | [CVPR](https://openaccess.thecvf.com/content/CVPR2022/papers/Li_DN-DETR_Accelerate_DETR_Training_by_Introducing_Query_DeNoising_CVPR_2022_paper.pdf) |
| DINO-5scale R50    |   36   |  51.2   |   47    | 2023.05 | [ICLR](https://openreview.net/pdf?id=3mRwyG5one) |
| Group-DINO-4scale R50    |   36   |  51.3   |   47    | 2023.10 | [ICCV](https://openaccess.thecvf.com/content/ICCV2023/papers/Chen_Group_DETR_Fast_DETR_Training_with_Group-Wise_One-to-Many_Assignment_ICCV_2023_paper.pdf) |
| Co-DINO-5scale-Deformable-DETR++ R50    |   36   |  54.8   |   47<sup>†</sup>    | 2023.10 | [ICCV](https://openaccess.thecvf.com/content/ICCV2023/papers/Zong_DETRs_with_Collaborative_Hybrid_Assignments_Training_ICCV_2023_paper.pdf) |
| RT-DETR R50 | 72 | 53.1 | 42 | 2024.06 | [CVPR](https://openaccess.thecvf.com/content/CVPR2024/papers/Zhao_DETRs_Beat_YOLOs_on_Real-time_Object_Detection_CVPR_2024_paper.pdf) |
| Relation-DETR R50 | 24 | 52.1 | 47<sup>†</sup> | 2024.10 | [ECCV](https://www.ecva.net/papers/eccv_2024/papers_ECCV/papers/06646.pdf) |
| Align-DETR R50 | 24 | 51.7 | 47<sup>†</sup> | 2024.11 | [BMVC](https://bmva-archive.org.uk/bmvc/2024/papers/Paper_211/paper.pdf) |
| D-FINE-X<sup>‡</sup> | 72 | 55.8 | 62 | 2025.04 | [ICLR](https://openreview.net/pdf?id=MFZjrTFE7h) |
| DEIM-D-FINE-X<sup>‡</sup> | 50 | 56.5 | 62 | 2025.06 | [CVPR](https://openaccess.thecvf.com/content/CVPR2025/papers/Huang_DEIM_DETR_with_Improved_Matching_for_Fast_Convergence_CVPR_2025_paper.pdf) |
| MI-DETR R50 | 24 | 52.7 | 47<sup>†</sup> | 2025.06 | [CVPR](https://openaccess.thecvf.com/content/CVPR2025/papers/Nan_MI-DETR_An_Object_Detection_Model_with_Multi-time_Inquiries_Mechanism_CVPR_2025_paper.pdf) |
| Mr. DETR (Align-DETR, R50) | 24 | 52.3 | 47<sup>†</sup> | 2025.06 | [CVPR](https://openaccess.thecvf.com/content/CVPR2025/papers/Zhang_Mr._DETR_Instructive_Multi-Route_Training_for_Detection_Transformers_CVPR_2025_paper.pdf) |

†: Estimation, ‡: Use HGNetv2 as backbone



### 성능 그래프

<div style="margin:18px 0">
  <canvas id="detrDateChart" height="280"></canvas>
</div>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script src="https://cdn.jsdelivr.net/npm/chartjs-adapter-date-fns@3"></script>

<script>
(function () {
  // === 1) 표 파싱 (열: Model | Epochs | AP | Params(M) | Date)
  const rows = Array.from(document.querySelectorAll("table tbody tr"));
  const raw = rows.map(tr => {
    const td = tr.querySelectorAll("td");
    return {
      model:  td[0].innerText.trim(),
      epochs: parseFloat(td[1].innerText),
      ap:     parseFloat(td[2].innerText),
      params: parseFloat(td[3].innerText),
      dateStr: (td[4]?.innerText || "").trim()  // "YYYY.MM"
    };
  }).filter(d => !Number.isNaN(d.ap));

  // === 2) YYYY.MM → Date 객체 (매월 1일로 처리)
  function ymToDate(s) {
    if (!s) return null;
    s = s.replace(/[^\d.\/\-]/g, '').trim();  // 숫자, ., -, / 외 문자 제거
    const m = s.match(/^(\d{4})[.\-\/](\d{1,2})$/);
    if (!m) return null;
    const y = Number(m[1]), mm = Number(m[2]);
    return new Date(y, mm - 1, 1);  // 매달 1일로 설정
  }

  // === 4) 날짜 버블 데이터 생성 (같은 달 겹침 줄이기 위해 약간의 x 지터)
  const jitter = (seed) => {
    // -5~+5 일 범위에서 소량 흔들기(시각화 전용)
    let h = 0; for (const ch of seed) h = (h * 131 + ch.charCodeAt(0)) >>> 0;
    return (h % 11) - 5; // -5..+5
  };

  // dot 크기 스케일 (루트 비례로 안정적)
  const rSize = (p) => Math.sqrt(p) * 2.0; // 2.5~3.5 사이로 조정해보세요

  const dateData = raw.map(d => {
    const dt = ymToDate(d.dateStr);
    return {
      x: dt ? new Date(dt.getFullYear(), dt.getMonth(), 1 + jitter(d.model)) : null,
      y: d.ap,
      r: rSize(d.params),
      _model: d.model,
      _params: d.params,
      _epoch: d.epochs,
      _date: d.dateStr
    };
  }).filter(d => d.x); // 날짜 없는 항목 제거

  // === 5) 날짜 차트 생성
  const ctx2 = document.getElementById('detrDateChart').getContext('2d');
  const dateChart = new Chart(ctx2, {
    type: 'bubble',
    data: {
      datasets: [{
        label: 'DETR-family (Date vs AP)',
        data: dateData,
        backgroundColor: 'rgba(59,130,246,0.28)',
        borderColor: 'rgba(59,130,246,0.55)',
        borderWidth: 1.5
      }]
    },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      plugins: {
        legend: { display: false },
        tooltip: {
          callbacks: {
            label: (ctx) => {
              const d = ctx.raw;
              return `${d._model} — Params ${d._params}M, AP ${d.y}`;
            }
          }
        },
      },
      scales: {
        x: {
          type: 'time',
          time: {
            unit: 'month',
            displayFormats: { month: 'yyyy.MM' }
          },
          min: new Date(2020, 0, 1),      // 👈 2020년 1월부터
          max: new Date(2026, 0, 1),      // 👈 2024년 1월까지
          title: { display: true, text: 'Date' },
          grid:  { color: 'rgba(0,0,0,0.08)' }
        },
        y: {
          title: { display: true, text: 'AP' },
          min: 40,  // 필요시 조절
          max: 60,
          ticks: { stepSize: 5 },
          grid:  { color: 'rgba(0,0,0,0.08)' }
        }
      }
    }
  });
})();
</script>

### 모델 분류표
DETR 후속 연구들은 attention 구조(self-attention 및 cross-attention) 를 개선하여 느린 수렴 문제를 해결하려는 방향으로 발전했다.
특히, decoder의 cross-attention이 encoder memory 전역에서 객체를 탐색하는 비효율성을 줄이기 위해, deformable sampling, conditional attention 등을 적용하여 수렴 속도를 크게 향상시켰다.

또한, object query를 단순한 random vector로 초기화하던 기존 방식 대신, box(anchor)나 reference point와 같은 물리적 의미를 가진 형태로 설계하여 학습 안정성과 해석 가능성을 높이는 연구들이 등장했다.
이로써 쿼리가 이미지 내의 실제 위치 정보를 직접 참조할 수 있게 되어, decoder가 불필요한 attention 탐색을 수행하지 않고 효율적으로 객체–특징 대응을 학습할 수 있게 되었다.

이후에는 DETR의 핵심인 bipartite matching(one-to-one) 전략의 한계를 보완하기 위해, one-to-many matching이나 auxiliary matching branch를 추가하는 연구들이 등장하였다.
이 방법들은 학습 안정성을 유지하고 one-to-one 매칭을 유지하여 nms와 같은 후처리가 필요없는 심플한 구조를 유지하면서도 recall과 convergence를 동시에 개선하려는 시도라 할 수 있다.

전체적으로 보면, DETR 계열의 발전 방향은 Object Query (입력 설계), Transformer Architecture (Attention 및 Decoder 구조), 그리고 Matching Mechanism (Loss 및 학습 전략) 의 세 축으로 구분하여 이해할 수 있다.

<p align="center">
  <img src="{{ '/assets/posts/2025-10-20-awesome-detr/detr.png' }}" width="90%">
</p>

## Reference
- [https://github.com/tyui592/awesome-detection-transformer](https://github.com/tyui592/awesome-detection-transformer)