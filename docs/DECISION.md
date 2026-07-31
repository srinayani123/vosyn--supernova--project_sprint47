# GLM adoption decision — Sprint 48 evidence record
Config: configs/baseline.json v1.0.0 | fingerprint 7f05cd7dba78
Supersedes: Sprint 47 exploration (5 inconsistent evals)
Quality metric used: chrF

## RECOMMENDATION: REPLACE baseline with GLM (provisional — human review pending)
Sign-off: ______   Date: ______

## Threshold scorecard
- [PASS] #1: chrF yue: glm=21.22 qwen=14.71
- [PASS] #2: %canto glm=93.5 qwen=81.5
- [PENDING] #3: PENDING
- [PASS] #4: chrF cmn: glm=37.02 qwen=35.53
- [PENDING] #5: PENDING
- [PASS] #6: 10.43 GB peak
- [PASS] #7: fert glm=1.5 qwen=1.385
- [PASS] #8: adapter hot-swap

## Automatic metrics (FLORES-200 devtest)
model          direction  chrF
  glm eng_Latn->yue_Hant 21.22
  glm eng_Latn->zho_Hans 37.02
  glm yue_Hant->eng_Latn 57.13
  glm zho_Hans->eng_Latn 58.23
 qwen eng_Latn->yue_Hant 14.71
 qwen eng_Latn->zho_Hans 35.53
 qwen yue_Hant->eng_Latn 57.09
 qwen zho_Hans->eng_Latn 57.61

## Cantonese authenticity screen
model  % genuine Cantonese  % Mandarin-in-Trad  % mixed  % neutral
  glm                 93.5                 0.5      0.5        5.5
 qwen                 81.5                 1.5      3.0       14.0

## Human review
{
  "eng_yue": {
    "n_scored": 0,
    "preferences": {
      "glm": 0,
      "qwen": 0,
      "tie": 0
    },
    "mean_scores": {}
  },
  "eng_cmn": {
    "n_scored": 0,
    "preferences": {
      "glm": 0,
      "qwen": 0,
      "tie": 0
    },
    "mean_scores": {}
  }
}

## Pipeline compatibility
{
  "tokenizer": {
    "glm": {
      "model": "zai-org/GLM-4-9B-0414",
      "fertility_yue": 1.5,
      "fertility_cmn": 0.614,
      "yue_vs_cmn_ratio": 2.443,
      "multi_token_particles": {
        "嘅": 2,
        "喺": 2,
        "咗": 2,
        "冇": 2,
        "啲": 2,
        "嗰": 2,
        "咁": 2,
        "哋": 2,
        "嚟": 2,
        "俾": 2,
        "睇": 2,
        "諗": 2,
        "搵": 2,
        "㗎": 3,
        "喎": 2,
        "囉": 2,
        "啫": 2,
        "嘞": 2,
        "咩": 2
      }
    },
    "qwen": {
      "model": "Qwen/Qwen2.5-7B-Instruct",
      "fertility_yue": 1.385,
      "fertility_cmn": 0.643,
      "yue_vs_cmn_ratio": 2.154,
      "multi_token_particles": {
        "嘅": 2,
        "喺": 2,
        "咗": 2,
        "冇": 2,
        "啲": 2,
        "嗰": 2,
        "咁": 2,
        "哋": 2,
        "嚟": 2,
        "諗": 2,
        "搵": 2,
        "㗎": 3,
        "喎": 2,
        "囉": 2
      }
    }
  },
  "qlora": {
    "status": "PASS",
    "target_modules": [
      "down_proj",
      "gate_up_proj",
      "k_proj",
      "o_proj",
      "q_proj",
      "v_proj"
    ],
    "trainable_params": 47595520,
    "one_step_loss": 3.9233,
    "peak_vram_gb": 10.43
  },
  "multi_lora": {
    "status": "PASS",
    "adapters_loaded": [
      "default",
      "yue_dialect"
    ],
    "hot_swap": "default -> yue_dialect -> default OK",
    "serving_plan": "one GLM base + per-dialect LoRA adapters; verify vLLM enable_lora on the deployment GPU"
  }
}

## Caveats
- FLORES yue_Hant references skew formal Standard Written Chinese; the human authenticity review is authoritative for colloquial Cantonese.
- Run used N_SENTENCES=200 — set 1012 (full devtest) before final sign-off.
- Sprint 47 baseline outputs were right-padded (corrupted batching); this run regenerated both systems left-padded under the frozen config.
- Valid ONLY for the pinned checkpoint + quantization; any change = new config version + full rerun.