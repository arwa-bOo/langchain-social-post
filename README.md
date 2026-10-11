# LangChain Social Post Generator

A small LangChain script for a Cairo social-media agency. It turns a short product description into a ready-to-paste Instagram/Facebook post as **structured JSON** (title, caption, hashtags, alt-text) plus an **Arabic caption**.

**Pipeline:** `ChatPromptTemplate` → Gemini with `with_structured_output(SocialPost)` → sequential step that translates `caption` to Arabic → `posts.csv`.

## Setup (free, $0)

1. Get a free API key from [Google AI Studio](https://aistudio.google.com) (no credit card).
2. Open `langchain_social_post.ipynb` in [Google Colab](https://colab.research.google.com).
3. In Colab, open **Secrets** (🔑 on the left), add a secret named `GEMINI_API_KEY` with your key, and turn on *Notebook access*. The key is read with `userdata.get("GEMINI_API_KEY")` and is never written in a code cell.
4. **Runtime → Run all.** The whole notebook runs in under 5 minutes.

## Exact install line

```
pip install -q langchain==1.4.4 langchain-google-genai==4.4.1 pydantic==2.14.0
```

## Model

`gemini-3.1-flash-lite` (Google AI Studio free tier).

The originally specified `gemini-2.0-flash` has been retired by Google and now returns 404. The notebook therefore auto-selects the first available Gemini flash model for the key (it tries `gemini-3.8-flash` first, then others) and prints `Using model: ...`. In the submitted run it selected `gemini-3.1-flash-lite`.

## The 3 test descriptions

1. `new iced karak chai with cardamom, served cold in a tall glass`
2. `handmade leather laptop sleeve in three colors, stitched by local artisans in Cairo`
3. `weekend special: warm koshari bowl with extra crispy onions and spicy tomato sauce`

## Structure

```
langchain-social-post/
├── README.md
├── langchain_social_post.ipynb   # Reqs 1, 2, 3
├── posts.csv                     # 3 generated posts incl. Arabic captions
└── screenshots/
    ├── 01_notebook_run.png
    ├── 02_structured_output.png
    └── 03_csv_open.png
```

## Design notes

- All prompts go through `ChatPromptTemplate` (no f-strings, no hard-coded prompt strings).
- `SocialPost` is a Pydantic `BaseModel`; `llm.with_structured_output(SocialPost)` returns a real `SocialPost` instance, checked with `isinstance`.
- A small validator guarantees every hashtag starts with `#`.
- `generate_post(description) -> dict` returns the post fields plus `arabic_caption`.
- `posts.csv` is saved as `utf-8-sig` so Arabic text displays correctly in Excel.

## Troubleshooting

- `404 / model not found` or quota errors: Google changes free-tier models over time. Re-check the key in AI Studio, wait a minute and re-run; if the model is retired, change only `MODEL_NAME` in cell 2.
- `SecretNotFoundError`: the secret name must be exactly `GEMINI_API_KEY` and Notebook access must be on.
