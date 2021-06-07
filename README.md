### 작성자 : LG CNS Enterprise분석3팀 한규인 사원
### 최초작성일 : 2021. 06. 04.

### 개 요

 - 해당 프로젝트는 Bert 모델을 구현하는 것을 목표로 합니다.
 - Bert-based를 Tensorflow v2, 한국어 기준으로 구현합니다.
 - 데이터는 Ko-Wiki 2021년 5월 20일 기준, 최신 위키 문서를 학습합니다.


### Feedback _ 06. 04

1. What is Pooler Layer
 - Pooler Layer는 모든 encoder Layer가 끝난 후에 첫 번째 토큰의 결과를 가져와서
   Dense Layer로 계산한 다음 Fine Tuning으로 쓰는 것 *Done*
2. 전처리 Library 사용 (문장 분절, 2Byte->1Byte, 단어 분절 등)
   wiki_detokenizer, ftfy
3. Max_Masked_Seq_Len를 이용한 Masked Batch Function 만들기, gather_nd, Loss function
4. Layer Normalization
5. 분산처리
6. TPU
7. tf.record
8. 잘 한 사람 코드 보고 배우기
9. Token화 하고 문장 잇기 (효율성)

- Longer sequences are disproportionately expensive because attention is quadratic to the sequence length.
To speed up pretraining in our experiments,
we pre-train the model with sequence length of 128 for 90% of the steps. Then, we train the rest 10% of the steps of sequence of 512 to learn the positional embeddings.
