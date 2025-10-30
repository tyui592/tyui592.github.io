---
layout: post
title: "Awesome Zero-shot VLM"
date: 2025-10-30 21:34:00 +0900
excerpt: "Zero-shot image classification VLM 성능 정리"
tags: [vlm, foundation-model, multi-modal, zero-shot, image_classification]
---
참고 논문: Vision-Language Models for Vision Tasks: A Survey (2024 TPAMI) ([arXiv](https://arxiv.org/abs/2304.00685), [github](https://github.com/jingyi0000/VLM_survey?tab=readme-ov-file))

위 참고 논문에는 대략 2023년까지의 논문들이 정리되어있다, 후속 연구들도 정리해두자.
우선 top-down으로 각 방법들의 성능에 대해 테이블과 그래프를 정리하고 세부사항에 대해서는 추후에 정리한다.

### Zero-shot image classification 성능 테이블
<div style="overflow-x: auto; width: 100%;">
<table id="vlm-performance-table">
<thead>
  <tr>
    <th>Methods</th>
    <th>Image encoder</th>
    <th>Text encoder</th>
    <th>Data Size</th>
    <th>ImageNet-1k</th>
    <th>CIFAR-10</th>
    <th>CIFAR-100</th>
    <th>Food101</th>
    <th>sun397</th>
    <th>Cars</th>
    <th>Aircraft</th>
    <th>DTD</th>
    <th>Pets</th>
    <th>caltech101</th>
    <th>flowers102</th>
  </tr>
</thead>
<tbody>
  <tr><td>CLIP<sup>1</sup></td><td>ViT-L/14</td><td>Transformer</td><td>400M</td><td>76.2</td><td>95.7</td><td>77.5</td><td>93.8</td><td>68.4</td><td>78.8</td><td>37.2</td><td>55.7</td><td>93.5</td><td>92.8</td><td>78.3</td></tr>
  <tr><td>DeCLIP<sup>2</sup></td><td>REGNET-Y</td><td>BERT</td><td>88M</td><td>73.7</td><td>-</td><td>-</td><td>-</td><td>-</td><td>-</td><td>-</td><td>-</td><td>-</td><td>-</td><td>-</td></tr>
  <tr><td>FILIP<sup>3</sup></td><td>ViT-L/14</td><td>Transformer</td><td>340M</td><td>77.1</td><td>95.7</td><td>75.3</td><td>92.2</td><td>73.1</td><td>70.8</td><td>60.2</td><td>60.7</td><td>92.0</td><td>93.0</td><td>90.1</td></tr>
  <tr><td>Florence<sup>4</sup></td><td>CoSwin</td><td>RoBERT</td><td>900M</td><td>83.7</td><td>94.6</td><td>77.6</td><td>95.1</td><td>77.0</td><td>93.2</td><td>55.5</td><td>66.4</td><td>95.9</td><td>94.7</td><td>86.2</td></tr>
  <tr><td>SLIP<sup>5</sup></td><td>ViT-L</td><td>Transformer</td><td>15M</td><td>47.9</td><td>87.5</td><td>54.2</td><td>69.2</td><td>56.0</td><td>9.0</td><td>9.5</td><td>29.9</td><td>41.6</td><td>80.9</td><td>60.2</td></tr>
  <tr><td>PyramidCLIP<sup>6</sup></td><td>ResNet50</td><td>T5</td><td>143M</td><td>47.8</td><td>81.5</td><td>53.7</td><td>67.8</td><td>65.8</td><td>65.0</td><td>12.6</td><td>47.2</td><td>83.7</td><td>81.7</td><td>65.8</td></tr>
  <tr><td>Chinese CLIP<sup>7</sup></td><td>ViT-L/14</td><td>CNRoberta</td><td>200M</td><td>-</td><td>96.0</td><td>79.7</td><td>-</td><td>-</td><td>-</td><td>26.2</td><td>51.2</td><td>-</td><td>-</td><td>-</td></tr>
  <tr><td>LiT<sup>8</sup></td><td>ViT-g/14</td><td>-</td><td>4B</td><td>85.2</td><td>-</td><td>-</td><td>-</td><td>-</td><td>-</td><td>-</td><td>-</td><td>-</td><td>-</td><td>-</td></tr>
  <tr><td>KELIP<sup>9</sup></td><td>ViT-B/32</td><td>Transformer</td><td>1.1B</td><td>62.6</td><td>91.5</td><td>68.6</td><td>79.5</td><td>-</td><td>75.4</td><td>-</td><td>51.2</td><td>-</td><td>-</td><td>-</td></tr>
  <tr><td>nCLIP<sup>10</sup></td><td>ViT/B/16</td><td>Transformer</td><td>35M</td><td>48.8</td><td>83.4</td><td>54.5</td><td>65.8</td><td>59.9</td><td>18.0</td><td>5.8</td><td>57.1</td><td>33.2</td><td>73.9</td><td>50.0</td></tr>
  <tr><td>NLIP<sup>11</sup></td><td>ViT-B/16</td><td>BART</td><td>26M</td><td>47.4</td><td>81.9</td><td>47.5</td><td>59.2</td><td>58.7</td><td>7.8</td><td>7.5</td><td>32.9</td><td>39.2</td><td>79.5</td><td>54.0</td></tr>
  <tr><td>UniCLIP<sup>12</sup></td><td>ViT-B/32</td><td>Transformer</td><td>30M</td><td>54.2</td><td>87.8</td><td>56.5</td><td>64.6</td><td>61.1</td><td>19.5</td><td>4.7</td><td>36.6</td><td>69.2</td><td>84.0</td><td>8.0</td></tr>
  <tr><td>RA-CLIP<sup>13</sup></td><td>ViT-B/32</td><td>BERT</td><td>15M</td><td>53.5</td><td>89.4</td><td>62.3</td><td>43.8</td><td>46.5</td><td>-</td><td>-</td><td>25.6</td><td>-</td><td>76.9</td><td>-</td></tr>
  <tr><td>LA-CLIP<sup>14</sup></td><td>ViT-B/32</td><td>Transformer</td><td>400M</td><td>64.4</td><td>92.4</td><td>73.0</td><td>79.7</td><td>64.9</td><td>81.9</td><td>20.8</td><td>55.4</td><td>87.2</td><td>91.8</td><td>70.3</td></tr>
  <tr><td>ALIP<sup>15</sup></td><td>ViT-B/32</td><td>Transformer</td><td>15M</td><td>40.3</td><td>83.8</td><td>51.9</td><td>45.4</td><td>47.8</td><td>3.4</td><td>2.7</td><td>23.2</td><td>30.7</td><td>74.1</td><td>54.8</td></tr>
  <tr><td>GrowCLIP<sup>16</sup></td><td>ViT-B/16</td><td>Transformer</td><td>12M</td><td>36.1</td><td>60.7</td><td>28.3</td><td>42.5</td><td>45.5</td><td>-</td><td>-</td><td>17.3</td><td>-</td><td>71.9</td><td>23.3</td></tr>
</tbody>
</table>
</div>

### 성능 비교 차트
(하단 모델명 클릭하여 on/off)

<div style="margin:20px 0; max-width: 900px; margin-left: auto; margin-right: auto;">
  <canvas id="vlmRadarChart" height="500"></canvas>
</div>

<div id="vlmLegend" style="text-align: center; margin-top: 10px; font-size: 0.85em;"></div>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<script>
(function () {
    // === 1. 데이터 추출 (테이블 ID로 선택) ===
    const table = document.getElementById('vlm-performance-table');
    if (!table) return;

    const rows = Array.from(table.querySelectorAll("tbody tr"));
    const dataLabels = [];
    const performanceLabels = [];
    let performanceIndexStart = -1;

    // 헤더에서 성능 컬럼 시작 위치와 이름 추출
    const headerCells = Array.from(table.querySelectorAll("thead th"));
    headerCells.forEach((th, index) => {
        const text = th.innerText.trim();
        if (text.startsWith('ImageNet-1k')) {
            performanceIndexStart = index;
        }
        if (performanceIndexStart !== -1 && index >= performanceIndexStart) {
            // [40] 등 괄호 안의 내용을 제거하고 레이블로 사용
            performanceLabels.push(text.replace(/ \[\d+\]/g, ''));
        }
    });

    // 데이터셋 추출
    const chartDatasets = [];
    rows.forEach(tr => {
        const tds = Array.from(tr.querySelectorAll("td"));
        if (tds.length < performanceIndexStart + 1) return;

        // const modelName = tds[0].innerText.trim().split(' ')[0]; // 모델명만 간단히 사용
        const modelName = tds[0].innerText.trim();
        dataLabels.push(modelName);

        const performanceData = [];
        for (let i = performanceIndexStart; i < tds.length; i++) {
            let value = tds[i].innerText.trim();
            // '-' 값은 null로 처리하여 차트에서 해당 지점을 그리지 않음
            performanceData.push(value === '-' ? null : parseFloat(value));
        }

        // 모든 성능 데이터가 null이 아닌 경우에만 차트 데이터셋에 포함 (선택적)
        if (performanceData.some(v => v !== null)) {
             chartDatasets.push({
                label: modelName,
                data: performanceData
            });
        }
    });
    
    // === 2. 차트 색상 및 스타일 정의 ===
    const dynamicColors = [
        'rgb(255, 99, 132)', 'rgb(54, 162, 235)', 'rgb(75, 192, 192)', 'rgb(255, 159, 64)',
        'rgb(153, 102, 255)', 'rgb(201, 203, 207)', 'rgb(255, 205, 86)', 'rgb(0, 128, 0)', 
        'rgb(128, 0, 0)', 'rgb(0, 0, 128)', 'rgb(128, 128, 0)', 'rgb(0, 128, 128)',
        'rgb(255, 0, 255)', 'rgb(0, 255, 255)', 'rgb(255, 255, 0)', 'rgb(100, 149, 237)',
    ];

    const chartDatasetsStyled = chartDatasets.map((item, index) => {
        const color = dynamicColors[index % dynamicColors.length];
        const colorAlpha = color.replace('rgb', 'rgba').replace(')', ', 0.2)'); // 배경 투명도 0.2
        const colorBorder = color.replace('rgb', 'rgba').replace(')', ', 1)');   // 선 투명도 1
        
        return {
            label: item.label,
            data: item.data,
            backgroundColor: colorAlpha,
            borderColor: colorBorder,
            pointBackgroundColor: colorBorder,
            pointBorderColor: '#fff',
            pointRadius: 3,
            borderWidth: 1.5,
            fill: true
        };
    });

    // === 3. 차트 렌더링 ===
    const ctx = document.getElementById('vlmRadarChart').getContext('2d');
    const vlmRadarChart = new Chart(ctx, {
        type: 'radar',
        data: {
            labels: performanceLabels,
            datasets: chartDatasetsStyled
        },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            plugins: {
                legend: { 
                    display: false // 커스텀 범례 사용을 위해 기본 범례 숨김
                },
                tooltip: {
                    callbacks: {
                        label: function(context) {
                            let label = context.dataset.label || '';
                            if (label) { label += ': '; }
                            if (context.raw !== null) {
                                label += context.raw.toFixed(1) + '%';
                            } else {
                                label += 'N/A';
                            }
                            return label;
                        }
                    }
                }
            },
            scales: {
                r: {
                    beginAtZero: true,
                    min: 0,
                    max: 100, // 최대 성능 100% 가정
                    ticks: {
                        stepSize: 20,
                        backdropColor: 'rgba(255, 255, 255, 0.7)',
                        font: { size: 10 }
                    },
                    pointLabels: {
                        font: { size: 11 }
                    },
                    grid: { color: 'rgba(0, 0, 0, 0.1)' },
                    angleLines: { color: 'rgba(0, 0, 0, 0.1)' }
                }
            }
        }
    });

    // === 4. 커스텀 범례 생성 ===
    const legendContainer = document.getElementById('vlmLegend');
    if (legendContainer) {
        chartDatasetsStyled.forEach(dataset => {
            const item = document.createElement('div');
            item.style.cssText = 'display: inline-flex; align-items: center; margin-right: 15px; cursor: pointer;';
            item.onclick = () => {
                const meta = vlmRadarChart.getDatasetMeta(vlmRadarChart.data.datasets.indexOf(dataset));
                meta.hidden = !meta.hidden;
                item.style.textDecoration = meta.hidden ? 'line-through' : 'none';
                vlmRadarChart.update();
            };

            const box = document.createElement('span');
            box.style.cssText = `display: inline-block; width: 12px; height: 12px; margin-right: 5px; background-color: ${dataset.borderColor}; border: 1px solid #333;`;

            const label = document.createElement('span');
            label.textContent = dataset.label;

            item.appendChild(box);
            item.appendChild(label);
            legendContainer.appendChild(item);
        });
    }

})();
</script>

### Dataset

| Name | Year | Num. of Image-Text Pairs | Language | Public |
| :--- | :---: | :---: | :---: | :---: |
| SBU Caption \[[Link](https://www.cs.rice.edu/~vo9/sbucaptions/)\] | 2011 | 1M | English | ✓ |
| COCO Caption \[[Link](https://github.com/tylin/coco-caption)\] | 2016 | 1.5M | English | ✓ |
| Yahoo Flickr Creative Commons 100 Million (YFCC100M) \[[Link](http://projects.dfki.uni-kl.de/yfcc100m/)\] | 2016 | 100M | English | ✓ |
| Visual Genome (VG) \[[Link](http://visualgenome.org/)\] | 2017 | 5.4M | English | ✓ |
| Conceptual Captions (CC3M) \[[Link](https://ai.google.com/research/ConceptualCaptions/)\] | 2018 | 3.3M | English | ✓ |
| Localized Narratives (LN) \[[Link](https://google.github.io/localized-narratives/)\] | 2020 | 0.87M | English | ✓ |
| Conceptual 12M (CC12M) \[[Link](https://github.com/google-research-datasets/conceptual-12m)\] | 2021 | 12M | English | ✓ |
| Wikipedia-based Image Text (WIT) \[[Link](https://github.com/google-research-datasets/wit)\] | 2021 | 37.6M | 108 Languages | ✓ |
| Red Caps (RC) \[[Link](https://redcaps.xyz/)\] | 2021 | 12M | English | ✓ |
| LAION400M \[[Link](https://laion.ai/blog/laion-400-open-dataset/)\] | 2021 | 400M | English | ✓ |
| LAION5B \[[Link](https://laion.ai/blog/laion-5b/)\] | 2022 | 5B | Over 100 Languages | ✓ |
| WuKong \[[Link](https://wukong-dataset.github.io/wukong-dataset/)\] | 2022 | 100M | Chinese | ✓ |
| CLIP | 2021 | 400M | English | ✗ |
| ALIGN | 2021 | 1.8B | English | ✗ |
| FILIP | 2021 | 300M | English | ✗ |
| WebLI | 2022 | 12B | 109 Languages | ✗ |

# Reference
1. CLIP: \[[ICML 2021](https://proceedings.mlr.press/v139/radford21a/radford21a.pdf)\] \[[Code](https://github.com/openai/CLIP)\] [Data: CLIP]
2. DeCLIP: \[[ICLR 2022](https://openreview.net/pdf?id=zq1iJkNk3uN)\] \[[Code](https://github.com/Sense-GVT/DeCLIP)\] [Data: CC3M, CC12M, YFCC100M, WIT]
3. FILIP:  \[[ICLR 2022](https://openreview.net/pdf/e8f6807c88ea1d0d0090f2c381f21739b217efb9.pdf)\] [Data: FILIP, CC3M, CC12M, YFCC100M]
4. Florence: \[[arXiv 2021](https://arxiv.org/pdf/2111.11432)\] [Data: FLD-900M]
5. SLIP: \[[ECCV 2022](https://link.springer.com/chapter/10.1007/978-3-031-19809-0_30)\], \[[arXiv](https://arxiv.org/pdf/2112.12750)\] \[[Code](https://github.com/facebookresearch/SLIP)\] [Data: YFCC100M]
6. PyramidCLIP: \[[NIPS 2022](https://proceedings.neurips.cc/paper_files/paper/2022/file/e9882f7f7c44a10acc01132302bac9d8-Paper-Conference.pdf)\] [Data: SBU, CC3M, CC12M, YFCC100M, LAION400M]
7. Chinese CLIP: \[[arXiv 2022](https://arxiv.org/pdf/2211.01335)\] \[[Code](https://github.com/OFA-Sys/Chinese-CLIP)\] [Data: LAION5B, WuKong, VG, COCO]
8. LiT: \[[CVPR 2022](https://openaccess.thecvf.com/content/CVPR2022/papers/Zhai_LiT_Zero-Shot_Transfer_With_Locked-Image_Text_Tuning_CVPR_2022_paper.pdf)\] \[[Project](https://google-research.github.io/vision_transformer/lit/)\] [Data: CC12M, YFCC100M, WIT]
9. KELIP: \[[ICLRW 2022](https://arxiv.org/pdf/2203.14463)\] \[[Code](https://github.com/navervision/KELIP)\] [Data: CUB200, WIT, YFCC15M, CC3M, CC12M, LAION400M, K-WIT]
10. nCLIP: \[[CVPR 2023](https://openaccess.thecvf.com/content/CVPR2023/papers/Zhou_Non-Contrastive_Learning_Meets_Language-Image_Pre-Training_CVPR_2023_paper.pdf)\] [Data: COCO, VG, SBU, CC3M, CC12M, YFCC14M]
11. NLIP: \[[AAAI 2023](https://ojs.aaai.org/index.php/AAAI/article/view/25172)\] [Data: YFCC100M, COCO]
12. UniCLIP: \[[NIPS 2022](https://proceedings.neurips.cc/paper_files/paper/2022/file/072fd0525592b43da661e254bbaadc27-Paper-Conference.pdf)\] [Data: CC3M, CC12M, YMCC100M]
13. RA-CLIP: \[[CVPR 2023](https://openaccess.thecvf.com/content/CVPR2023/papers/Xie_RA-CLIP_Retrieval_Augmented_Contrastive_Language-Image_Pre-Training_CVPR_2023_paper.pdf)\] [Data: YFCC100M]
14. LA-CLIP: \[[NIPS 2023](https://proceedings.neurips.cc/paper_files/paper/2023/file/6fa4d985e7c434002fb6289ab9b2d654-Paper-Conference.pdf)\] \[[Code](https://github.com/LijieFan/LaCLIP)\] [Data: CC3M, CC12M, RC, LAION400M]
15. ALIP: \[[ICCV 2023](https://openaccess.thecvf.com/content/ICCV2023/papers/Yang_ALIP_Adaptive_Language-Image_Pre-Training_with_Synthetic_Caption_ICCV_2023_paper.pdf)\] \[[Code](https://github.com/deepglint/ALIP)\] [Data: YFCC100M]
16. GrowCLIP: \[[ICCV 2023](https://openaccess.thecvf.com/content/ICCV2023/papers/Deng_GrowCLIP_Data-Aware_Automatic_Model_Growing_for_Large-scale_Contrastive_Language-Image_Pre-Training_ICCV_2023_paper.pdf)\] [Data: CC12M]