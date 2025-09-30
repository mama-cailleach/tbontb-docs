# Phase 4 - Dashboard Plan

## Decision #1 
### Plattform decision

### Platform Analysis: Streamlit vs Alternatives

| Platform | Pros | Cons | Recommendation |
|----------|------|------|----------------|
| Streamlit | • Python-based<br>• Easy to learn<br>• Free hosting<br>• Good mobile support<br>• Great for portfolio	| Less interactive than BI tools<br>• Some layout limitations | ✅ Best for needs |
| Power BI | • Powerful visualizations<br>• Business-friendly | • Learning curve<br>• Costs for full features<br>• Less portfolio value |	Not ideal for this project |
| Tableau | • Industry standard<br>• Beautiful visuals | • Expensive<br>• Steep learning curve | Overkill for project needs |
| Dash (Plotly) | • Python-based<br>• Highly customizable | • More complex than Streamlit<br>• Harder to host | Consider for advanced features later |

#### Choosen: Streamlit 
 For ease of use, acessiblity, cost and hosting. Also for python learning and a possible upgrade later with plotly or something else python related.

---
 
## Step-by-Step Plan

1. Start Simple
    - Install Streamlit (pip install streamlit pandas plotly)
    - Create basic app structure with sample data
    - Focus on a single feature first (player statistics search)

2.  Core Features
    - Set up multi-page navigation (using Streamlit's built-in system)
    - Create these initial pages:
    - Home: Team overview stats and recent performance
    - Player Stats: Search by name, view career statistics
    - Team Records: Important milestones and achievements

3. Visualizations
    - Add charts for each section:
    - Player performance trends
    - Team win/loss charts
    - Venue performance comparisons
    - Ensure mobile responsiveness

4. Deploy & Share
    - Deploy to Streamlit Cloud (free hosting)
    - Share with team for feedback
    - Iterate based on user suggestions

---

## Getting Started Code

### Here's a simple starter template for your app:

```
# app.py
import streamlit as st
import pandas as pd
import plotly.express as px

# Page configuration
st.set_page_config(page_title="TBONTB Cricket Dashboard", page_icon="🏏", layout="wide")

# Sidebar navigation
st.sidebar.title("Navigation")
page = st.sidebar.radio("Select Page", ["Team Overview", "Player Statistics", "Team Records"])

# Load data
@st.cache_data
def load_data():
    batting_df = pd.read_csv("TBONTB_all_batting.csv")
    return batting_df

try:
    batting_data = load_data()
    
    # Page content
    if page == "Team Overview":
        st.title("TBONTB Cricket Team Dashboard")
        st.subheader("Team Performance")
        
        # Quick stats
        matches = batting_data['match_id'].nunique()
        total_runs = batting_data['runs'].sum()
        
        col1, col2, col3 = st.columns(3)
        col1.metric("Total Matches", matches)
        col2.metric("Total Runs", total_runs)
        
        # Basic chart
        seasons = batting_data['Season'].unique()
        season_runs = batting_data.groupby('Season')['runs'].sum().reset_index()
        fig = px.line(season_runs, x='Season', y='runs', title="Runs by Season")
        st.plotly_chart(fig)
        
    elif page == "Player Statistics":
        st.title("Player Statistics")
        
        # Player selection
        all_players = sorted(batting_data['player_name'].unique())
        selected_player = st.selectbox("Select Player", all_players)
        
        # Player stats
        player_data = batting_data[batting_data['player_name'] == selected_player]
        total_runs = player_data['runs'].sum()
        innings = len(player_data)
        
        col1, col2 = st.columns(2)
        col1.metric("Total Runs", total_runs)
        col2.metric("Innings", innings)
        
        # Player chart
        fig = px.bar(player_data, x='match_id', y='runs', title=f"{selected_player}'s Runs by Match")
        st.plotly_chart(fig)
        
    elif page == "Team Records":
        st.title("Team Records & Milestones")
        # Records content here
        
except Exception as e:
    st.error(f"Error loading data: {e}")
    st.info("Please make sure your CSV files are in the correct location.")

```

---

## Next Decisions
### Before jumping in, consider these important decisions:

- Data Structure: Will you use separate CSVs (batting, bowling, matches) or combine them?
- Update Frequency: How often will you update the dashboard with new match data?
- Features Priority: Which features would your teammates value most?
- Filtering Needs: What filtering options are most important (seasons, venues, opponents)?

---

## First Action Items

### - Set up your environment: Install Streamlit and required packages
### - Create a project repository: Set up GitHub repo for version control
### - Build a minimal prototype: Start with just the player search feature
### - Test with sample data: Before connecting all your data

---

# Phase 4 Dashboard Plan - Refined Approach

## Data Structure Decision
### Recommendation: Start with separate CSVs but create helper functions

```

# Helper function example for your app.py
@st.cache_data
def load_and_process_data():
    # Load separate files
    batting_df = pd.read_csv("TBONTB_all_batting.csv")
    bowling_df = pd.read_csv("TBONTB_all_bowling.csv")
    matches_df = pd.read_csv("TBONTB_match_info.csv")
    
    # Create joined views as needed
    player_combined = pd.merge(
        batting_df.groupby(['player_name', 'Season'])['runs'].sum().reset_index(),
        bowling_df.groupby(['player_name', 'Season'])['wickets'].sum().reset_index(),
        on=['player_name', 'Season'], how='outer'
    ).fillna(0)
    
    return {
        "batting": batting_df,
        "bowling": bowling_df, 
        "matches": matches_df,
        "player_combined": player_combined
    }

```

#### Why this approach works:

- Keeps your data organization clean
- Creates derived views only when needed
- Caching means the joins only happen once per session
- More flexible as your needs evolve

---

### Feature Prioritization

Based on your team needs, I'd recommend this sequence:

1. Player Search & Stats (MVP)

- Player dropdown with season filter
- Basic career stats + visualizations
- Games played filter (slider: "Show players with 3+ games")
- Team Overview

2. Season performance trends
- Win/loss breakdown
- Key milestone tracking
- Advanced Filtering

3. Venue performance
- Opposition analysis
- Multi-dimension filtering
- Feedback Collection

4. Feedback Collection

```

# Simple feedback form
with st.expander("Share Feedback or Feature Requests"):
    feedback_name = st.text_input("Your Name")
    feedback_type = st.selectbox("Feedback Type", ["Bug Report", "Feature Request", "General Feedback"])
    feedback_text = st.text_area("Your Feedback")
    
    if st.button("Submit Feedback"):
        # Save to CSV or send email
        feedback_df = pd.DataFrame({
            "timestamp": [datetime.now()],
            "name": [feedback_name],
            "type": [feedback_type],
            "feedback": [feedback_text]
        })
        feedback_df.to_csv("feedback.csv", mode='a', header=False, index=False)
        st.success("Thanks for your feedback!")

```

---

## Modified Structure for Your App
### Let's refine the structure to reflect these decisions:

```

# More organized app structure

import streamlit as st
import pandas as pd
import plotly.express as px
from datetime import datetime

# Page configuration
st.set_page_config(page_title="TBONTB Cricket Dashboard", page_icon="🏏", layout="wide")

# Data loading with cache
@st.cache_data
def load_data():
    # Your data loading logic
    # ...
    return data_dict

# App header and description
st.title("🏏 TBONTB Cricket Dashboard")
st.markdown("*Team stats from 2013-2025 seasons*")

# Sidebar filters (apply across all pages)
st.sidebar.title("Filters")

# Main navigation after filters
st.sidebar.title("Navigation")
page = st.sidebar.radio("Select Page", ["Team Overview", "Player Statistics", "Venue Analysis", "Submit Feedback"])

# Page content based on selection
if page == "Player Statistics":
    # Player filtering section
    st.header("Player Statistics")
    
    # Games played filter
    min_games = st.slider("Minimum Games Played", 1, 50, 3)
    
    # Season filter
    all_seasons = data["batting"]["Season"].unique().tolist()
    selected_seasons = st.multiselect("Select Seasons", all_seasons, default=all_seasons)
    
    # Filter players by games threshold
    filtered_players = # Your filtering logic
    
    # Rest of player stats page
    # ...

```


---

## Next Steps

### 1. Environment Setup (Day 1-2)
- Install packages: pip install streamlit pandas plotly
- Create GitHub repo: github.com/your-username/tbontb-dashboard
- Initial directory structure:
    ```
    /tbontb-dashboard
  /data           # Your CSV files
  /pages          # For multi-page apps
  app.py          # Main file
  README.md       # Documentation
    
    ```


### 2. Data Preparation (Day 3-5)
- Export clean CSVs from your Excel workbooks
- Create test datasets for development
- Document data dictionary (column meanings)


### 3. MVP Development (Week 1-2)
- Build player search feature
- Implement basic filtering
- Create simple visualizations


---

## Virtual Enviroment Set up:


### Why Use a Virtual Environment?
1. **Project Isolation**: Keeps dependencies for different projects separate
2. **Reproducibility**: Makes it easier to recreate your environment on different machines
3. **Dependency Management**: Prevents conflicts between package versions
4. **Deployment Ready**: Streamlit Cloud and other hosting platforms work best with requirements.txt from virtual environments
5. **Clean System**: Doesn't pollute your global Python installation


Setting up:

```

# 1. Navigate to your project directory
cd d:\Cricket_Data\TBONTB\dashboard

# 2. Create a virtual environment
python -m venv venv

# 3. Activate the virtual environment
# On Windows:
venv\Scripts\activate
# On macOS/Linux:
# source venv/bin/activate

# 4. Install required packages
pip install streamlit pandas plotly

# 5. Save your dependencies
pip freeze > requirements.txt

# 6. To run
streamlit run app.py

```