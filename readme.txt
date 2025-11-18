================================================================================
                        REBALANCEAI
        AI-POWERED INVESTMENT PORTFOLIO ANALYZER
        
                  Hackathon Submission - October 2025
================================================================================

TEAM INFORMATION
----------------
Team Member(s): Nishant Upadhyay
		Aditya Kumar
		Sailesh Ananthanarayanan

PROJECT SUMMARY
---------------
RebalanceAI is a LangChain-powered portfolio analyzer that combines 
quantitative finance metrics with real-time sentiment analysis to generate 
explainable, risk-aware investment recommendations.

Key Innovation: Historical backtesting tool for portfolio validation

================================================================================
TABLE OF CONTENTS
================================================================================

1. Setup Instructions
2. Architecture & LangChain Implementation
3. Methodology (Metrics & Sentiment Weighting)
4. Innovation: Backtesting Feature
5. Results & Screenshots
6. Deliverables Checklist
7. Responsible AI Notice

================================================================================
1. SETUP INSTRUCTIONS
================================================================================

PREREQUISITES
-------------
* Python 3.8+
* Google Colab or Jupyter Notebook
* API Keys: OpenAI, Anthropic, NewsAPI (all have free tiers)

INSTALLATION (GOOGLE COLAB - RECOMMENDED)
------------------------------------------

Step 1: Install Dependencies
!pip install langchain langchain-openai langchain-anthropic
!pip install yfinance newsapi-python pandas numpy matplotlib

Step 2: Add API Keys to Colab Secrets
1. Click key icon in left sidebar
2. Add three secrets:
   - openai_api: [your OpenAI key]
   - anthropic_api: [your Anthropic key]
   - NEWSAPI_K: [your NewsAPI key]

Step 3: Run Notebook
- Open rebalance_ai.ipynb
- Run all cells (Runtime > Run all)
- Test with sample queries

API KEY LINKS
-------------
OpenAI: https://platform.openai.com/api-keys (free credits)
Anthropic: https://console.anthropic.com/ ($5 free)
NewsAPI: https://newsapi.org/register (100 req/day free)

================================================================================
2. ARCHITECTURE & LANGCHAIN IMPLEMENTATION
================================================================================

SINGLE AGENT DESIGN (40% OF GRADE)
-----------------------------------

Component: LangChain ReAct Agent
LLMs: GPT-4 (primary), Claude-3-Haiku (comparison)
Tools: 5 specialized tools
Memory: ConversationBufferMemory for context
Prompt: Custom ReAct template with investment expertise

TOOL SPECIFICATIONS
-------------------

1. fetch_stock_data
   - Input: Ticker symbol (e.g., 'AAPL')
   - Output: Price, Sharpe ratio, volatility, 52w high/low
   - Data Source: yfinance

2. analyze_sentiment
   - Input: Ticker + company name
   - Output: Recent headlines (7-day window) + sentiment note
   - Data Source: NewsAPI

3. compare_stocks
   - Input: Comma-separated tickers
   - Output: Side-by-side comparison table
   - Data Source: yfinance

4. optimize_portfolio
   - Input: Tickers, amount, risk profile
   - Output: Recommended allocation with reasoning
   - Logic: Risk-based weighting (see Methodology)

5. backtest_portfolio (INNOVATION)
   - Input: Tickers, weights, start date
   - Output: Historical performance metrics
   - Logic: 2+ years validation, max drawdown, Sharpe ratio

AGENT WORKFLOW
--------------
1. User submits natural language query
2. Agent selects appropriate tool(s)
3. Tools fetch/analyze data
4. Agent synthesizes results
5. Returns explained recommendation

LANGCHAIN FEATURES DEMONSTRATED
--------------------------------
* ReAct agent pattern (reasoning + acting)
* Tool orchestration (multiple tool calls per query)
* Prompt engineering (investment expert persona)
* Error handling (graceful degradation)
* Multi-LLM support (GPT-4 vs Claude comparison)

================================================================================
3. METHODOLOGY (25% OF GRADE)
================================================================================

QUANTITATIVE METRICS
--------------------

Returns:
  Period Return = (end_price / start_price - 1) × 100%
  Annualized Return = mean_daily_return × 252 trading days

Risk:
  Volatility = std_daily_returns × √252 (annualized)
  Max Drawdown = min((cumulative_returns / cummax - 1))

Risk-Adjusted:
  Sharpe Ratio = (mean_return / std_return) × √252
  Assumes risk-free rate = 0 for simplicity

PORTFOLIO OPTIMIZATION WEIGHTING
---------------------------------

Conservative (Low Risk):
  weight_i = (1 / volatility_i) / Σ(1 / volatility_j)
  Rationale: Inverse volatility weighting minimizes risk

Moderate (Balanced):
  weight_i = (sharpe_i × (1/vol_i)) / Σ(sharpe_j × (1/vol_j))
  Rationale: Balance risk-adjusted returns with risk minimization

Aggressive (High Return):
  weight_i = sharpe_i / Σ(sharpe_j)
  Rationale: Maximize risk-adjusted returns

All weights normalized to sum to 1.0

SENTIMENT INTEGRATION WEIGHTING
--------------------------------

Formula:
  Final Score = (0.70 × Quantitative) + (0.30 × Sentiment)

Quantitative (70%):
  - Based on Sharpe ratio, volatility, returns
  - Historical performance data
  - Technical indicators

Sentiment (30%):
  - LLM analysis of recent news headlines (7-day window)
  - Bullish/bearish classification
  - NewsAPI sources: WSJ, Bloomberg, CNBC, Reuters

Rationale: Fundamentals drive long-term performance, but sentiment 
captures short-term momentum and market psychology.

DATA SOURCES
------------
* yfinance: Historical prices, company info (15-min delay, free)
* NewsAPI: News headlines (100 requests/day, free tier)

================================================================================
4. INNOVATION: BACKTESTING FEATURE (15% OF GRADE)
================================================================================

WHAT IT DOES
------------
Validates portfolio strategies using 2+ years of historical market data.
Goes beyond current metrics to show actual past performance.

WHY IT'S INNOVATIVE
-------------------
* Most portfolio tools only show current metrics
* Backtesting reveals worst-case scenarios (max drawdown)
* Builds confidence in recommendations with evidence
* Educational: shows portfolio behavior during corrections

IMPLEMENTATION
--------------
1. Fetch historical data (2+ years)
2. Calculate daily returns
3. Apply portfolio weights
4. Compute cumulative returns
5. Measure: total return, Sharpe ratio, max drawdown

TEST RESULTS
------------
Portfolio: 60% AAPL, 40% MSFT
Period: Jan 2022 - Oct 2025 (952 days)

Metrics:
- Total Return: 51.77%
- Annualized Return: 14.35%
- Sharpe Ratio: 0.58
- Max Drawdown: -28.4% (Q4 2022)
- $1,000 → $1,517.70

Insight: Strong performance despite 2022 tech downturn, validating 
moderate-risk suitability.

================================================================================
5. RESULTS & SCREENSHOTS
================================================================================

MULTI-LLM COMPARISON RESULTS
-----------------------------

Test Query: "Analyze Tesla (TSLA) for moderate-risk investor"

Evaluation (8 criteria, scale 1-5):

Criteria             | GPT-4 | Claude | Winner
---------------------|-------|--------|--------
Clarity              |   5   |   5    |  TIE
Accuracy             |   5   |   4    |  GPT-4
Tool Orchestration   |   5   |   3    |  GPT-4
Sentiment Quality    |   4   |   4    |  TIE
Actionability        |   5   |   3    |  GPT-4
User Communication   |   4   |   5    |  CLAUDE
Error Handling       |   4   |   5    |  CLAUDE
Risk Awareness       |   4   |   5    |  CLAUDE
---------------------|-------|--------|--------
AVERAGE              | 4.50  | 4.25   |  GPT-4

Conclusion: GPT-4 better for data-intensive analysis, Claude better 
for error handling and transparent communication.

SAMPLE PORTFOLIO ANALYSIS
--------------------------

Input: $10,000, moderate risk, tech stocks (AAPL, MSFT, NVDA, GOOGL)

Output:
  AAPL:  $3,000 (30%) - Stability
  MSFT:  $3,000 (30%) - Balance
  NVDA:  $2,500 (25%) - Growth
  GOOGL: $1,500 (15%) - Diversification
  
  Expected Return: 24.3%
  Volatility: 28.7%
  Sharpe Ratio: 0.85

Visualization: See portfolio_analysis.png
- Panel 1: Allocation pie chart
- Panel 2: 1-year performance comparison
- Panel 3: Risk-return scatter plot
- Panel 4: Dollar allocation bars

================================================================================
6. DELIVERABLES CHECKLIST
================================================================================

REQUIRED FILES
--------------
✓ rebalance_ai.ipynb         - Complete implementation with 18 cells
✓ README.txt                  - This file (setup, methodology, results)
✓ requirements.txt            - Python dependencies
✓ RebalanceAI_Presentation.pptx - Presentation slides
✓ demo_video.mp4              - 6-minute video demonstration
✓ portfolio_analysis.png      - Visualization output

OPTIONAL FILES
--------------
✓ .env.example                - API key template

SUBMISSION FORMAT
-----------------
Zip file name: Team[#]_RebalanceAI_Hackathon.zip
Contains all files above

SAMPLE DATA NOTE
----------------
Sample cached data not required because:
- yfinance is unlimited and free (no API key needed)
- NewsAPI free tier (100 req/day) sufficient for testing
- All tools include error handling
- Code fully reproducible with grader's own API keys

================================================================================
7. RESPONSIBLE AI NOTICE
================================================================================

⚠ IMPORTANT DISCLAIMERS

This tool provides EDUCATIONAL INSIGHTS ONLY and does NOT constitute 
financial advice.

* We are NOT licensed financial advisors
* All recommendations are for informational purposes only
* Past performance does not guarantee future results
* Users can lose their entire investment
* Always consult licensed professionals before investing

Data Privacy:
- No user data stored
- All analyses are session-based
- API keys user-provided, not logged

Transparency:
- yfinance has 15-minute delays
- NewsAPI limited to 100 requests/day
- Simplified assumptions (zero risk-free rate)
- Model uncertainties acknowledged


================================================================================
TECHNICAL SUPPORT
================================================================================

Common Issues:

Issue: API rate limits
Solution: Wait 24 hours or use different API key

Issue: yfinance data errors
Solution: Tool includes fallback to period='2y' automatically

Issue: Claude iteration timeout
Solution: Increased max_iterations to 15, early_stopping enabled

Issue: Visualization not displaying
Solution: Ensure matplotlib installed, use plt.show()
