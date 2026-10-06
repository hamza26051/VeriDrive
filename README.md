# VeriDrive

> An MLOps prototype for analysing public social content as one candidate risk signal—with evaluation, monitoring, and human-review paths in the loop.

VeriDrive is a full-stack experiment in what happens after a text model leaves a notebook. It combines a FastAPI service, a modern frontend, configurable model loading, persistence, testing, containerisation, and a small model-operations workflow around sentiment and toxicity signals.

## What the prototype does

- Accepts a small set of text posts and an optional bio, either through manual entry or a Mastodon OAuth demonstration flow.
- Preprocesses text and combines toxicity and sentiment signals into an experimental profile-level score, confidence, and explanation list.
- Persists analysis records and notifications through Firebase services so a reviewer can inspect outcomes later.
- Provides admin-oriented review and override endpoints rather than treating a raw model response as the whole workflow.
- Includes the engineering around the model: tests, Docker packaging, GitHub workflows, model-promotion helpers, MLflow hooks, continuous-training utilities, and drift-monitoring code.

## The shape of the system

```text
posts / profile bio
        ↓
normalisation → toxicity + sentiment signals → combined score + reasons
        ↓                                          ↓
FastAPI API  ←→  frontend / Mastodon demo     Firestore review record
        ↓
tests · containers · monitoring · model lifecycle helpers
```

## Repository guide

| Area | Purpose |
| --- | --- |
| `main.py` | FastAPI routes for analysis, OAuth demonstration, admin review, and user records |
| `risk_engine.py` | Text cleaning, model inference, signal combination, and explanation logic |
| `train_sentiment.py`, `transformers_sentiment.py` | Training and transformer-oriented model work |
| `drift_monitor.py`, `ct_loop.py`, `promote_model.py` | Monitoring, feedback, and model-lifecycle experiments |
| `.github/workflows/` and tests | CI coverage and regression checks |
| `frontend/` | The product-facing interface |

## Run it locally

    python -m venv .venv
    .venv\Scripts\activate        # Windows
    pip install -r requirements.txt
    uvicorn main:app --reload

For connected features, configure a local environment file with your own Firebase credentials, Mastodon OAuth client values, admin secret, and frontend URL. Do not deploy the development defaults in `config.py`; production secrets belong in the environment or a secret manager, never in the repository.

## A necessary boundary

This is a learning and product-engineering prototype, not a system for making automatic decisions about a person’s eligibility, price, access, trustworthiness, or character. Some experimental code uses car-rental approval and rejection labels; those labels describe an early scenario, not a safe real-world policy. Sentiment and toxicity classifiers can be wrong, biased, context-blind, and easy to misread.

Any serious deployment would need purpose limitation, consent and data-minimisation rules, representative evaluation, independent fairness testing, appeal and correction paths, security review, and trained humans with real authority to override the model. Until then, its most useful role is showing the technical and operational questions that responsible ML systems must answer.

## Why it belongs in this portfolio

The point of VeriDrive is the full lifecycle: a model needs more than accuracy. It needs an interface, traces, tests, deployment discipline, a way to notice drift, and a human process for disagreement. This repository keeps that whole conversation visible.
