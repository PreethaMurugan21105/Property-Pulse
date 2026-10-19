# Property Pulse

Property Pulse is a real-estate decision-support application. It brings property price estimation, property exploration, market visualizations, and saved-property tools together in one interface. It is designed to help users compare property information and understand factors that may affect a property's value.

## The problem it solves

Property information is often spread across separate tools: users may need one place to estimate a price, another to explore locations, and another to keep track of interesting properties. Property Pulse combines these activities so users can:

- Estimate a property's value from its characteristics.
- Explore sample property data on maps and in analytics views.
- Compare a prediction with related properties.
- Save properties and manage an account.
- Ask a real-estate assistant questions and view property-related news.

Estimates and sample-data visualizations are informational tools, not professional appraisals or financial advice.

## How it works

```text
User
 ├─ Property exploration ──> React pages ──> public property CSV
 │                                      └──> maps, heatmap, analytics, comparisons
 ├─ Price estimate ────────> FastAPI /api/estimate
 │                            └──> feature processing + scaler + XGBoost model
 │                                  └──> estimate and market/property insights
 ├─ Chat ──────────────────> FastAPI /chat ──> Gemini or fallback responses
 ├─ Account and saved data > Firebase Authentication / Firestore
 └─ News ──────────────────> Vercel /api/news ──> NewsAPI
```

### Price-estimation workflow

1. The user enters property details in the prediction form.
2. The frontend sends those features to the backend's `/api/estimate` endpoint.
3. FastAPI validates the request. The backend derives model inputs and applies the saved preprocessing artifacts and XGBoost model.
4. The API returns a predicted price, confidence and model information, market analysis, and feature insights.
5. The frontend keeps the request and result in prediction context so the results screen and related-property features can use them.

The model and preprocessing files are stored under `backend/model/artifacts_v2/`. Model output depends on the submitted features and the model artifacts; it should be treated as an estimate.

### Property exploration workflow

The frontend reads `public/data/House_Price_India.csv` for property records. Pages and components use these records to power map markers, heatmaps, analytics, property details, and related-property comparisons. This browser-loaded dataset is separate from the backend model artifacts used for price prediction.

### Supporting workflows

- **Accounts:** Firebase Authentication supports email/password and Google sign-in. Firestore supports account-related data.
- **Assistant:** the chatbot sends a message and optional context to `/chat`. The backend uses Gemini when available and includes fallback response handling.
- **News:** the news component requests `/api/news`. The Vercel serverless function makes the NewsAPI request server-side.

## Technology stack

| Area | Technologies |
| --- | --- |
| Frontend | React 18, TypeScript, Vite |
| Navigation and state | React Router, React Context, TanStack Query |
| UI and styling | Tailwind CSS, shadcn/ui, Radix UI |
| Maps and visualizations | Leaflet, React Leaflet, Recharts |
| Backend API | Python, FastAPI, Pydantic |
| Prediction | XGBoost, scikit-learn, pandas, NumPy, joblib |
| Identity and data | Firebase Authentication, Cloud Firestore |
| AI and news | Gemini API, NewsAPI via a Vercel serverless function |

## Application areas

- **Property prediction:** collect property characteristics and display an estimate with analysis.
- **Maps and heatmap:** visualize property locations and dataset values spatially.
- **Analytics and dashboard:** summarize and explore property data.
- **Property comparison and favorites:** review related records and keep track of selected properties.
- **Profile and authentication:** manage a signed-in user experience.
- **Chatbot and news:** provide a real-estate question-and-answer interface and relevant articles.

The frontend routes include `/`, `/predict`, `/heatmap`, `/analytics`, `/dashboard`, `/favorites`, `/profile`, `/signin`, `/signup`, and `/chatbot`.

## Main components

```text
api/                 Vercel serverless functions, including the news endpoint
backend/             FastAPI application, prediction logic, and model artifacts
public/data/         Property CSV used by frontend exploration features
src/pages/           Route-level screens
src/components/      Forms, maps, charts, and shared interface components
src/contexts/        Authentication, language, and prediction state
src/lib/             API clients, Firebase setup, and property data services
src/types/           Shared TypeScript data types
```

## External services and configuration

The application integrates with Firebase, Gemini, and NewsAPI. Their credentials are provided through environment configuration rather than committed source files. Prediction API requests use the backend URL configured in `src/lib/api.ts`; the chatbot currently targets `http://localhost:8000/chat` directly.
