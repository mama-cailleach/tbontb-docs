# 🧠 Cricket Workbook – Technical Implementation

## 📁 Architecture & File Structure
```
Cricket_Stats_Season_YYYY.xlsx
├── season_summary        # Match metadata
├── all_batting          # Player batting records  
├── all_bowling          # Player bowling records
├── M001_stats           # Individual match breakdown
├── M002_stats           # Individual match breakdown
├── M00X_stats           # ... (one per match)
├── team_players         # TBONTB roster (formula-heavy)
├── team_players_clean   # TBONTB roster (values only, shareable)
├── opposition_players   # Opposition rosters (formula-heavy)
├── opposition_players_clean # Opposition rosters (values only, shareable)
├── visuals             # Dashboard (in development)
└── lookup_tables       # ID mappings
```

## 🐍 Python Processing Pipeline

### Data Flow
1. **Input**: Raw Excel sheets (`raw_paste_data_M001`, `raw_paste_data_M002`, etc.)
2. **Processing**: Extract match info from cell A10 (A9 pre-2016), parse batting/bowling blocks
3. **VBA Standardization**: Historical data correction for consistent cell references
4. **Output**: Structured DataFrames for import

### Output Structure
- **Individual match sheets**: `M001_stats`, `M002_stats`, etc. containing:
  - Vertical match info (Field/Value columns) including Player of the Match
  - Batting scorecard for both teams (no extras row)
  - Bowling figures for both teams (extras included as team concessions)
- **Combined sheets**: Aggregated data across all matches
- **Player sheets**: Separate internal vs. shareable versions

### Key Functions
- `process_match()` - Main orchestrator
- `parse_batting()` - Handles separate name/dismissal rows
- `extract_metadata()` - Pulls match details with date formatting (yyyy-mm-dd)
- `parse_bowling()` - Includes extras as bowling team concessions
- `extract_extras_and_dnb()` - Separates extras and Did Not Bat players

## 🔣 Excel Formula Patterns

### ID Lookups
```excel
=XLOOKUP([@Opponent],OppositionList,IDList)
=INDEX(PlayerList,MATCH([@Player],PlayerList,0))
```

### Cricket-Specific Calculations
```excel
# Base-5 overs for The Hundred
=INT(Overs)+MOD(Overs,1)*10/5

# Match ID generation  
="M"&TEXT(ROW()-2,"000")

# Economy rate calculation
=IF(Overs>0,RunsConceded/Overs,"")

# Strike rate calculation  
=IF(Balls>0,(Runs/Balls)*100,"")
```

### Aggregation Formulas
```excel
=SUMIFS(RunsRange,PlayerRange,"Player Name",MatchRange,"M001")
=UNIQUE(FILTER(VenueRange,VenueRange<>""))
```

## 🔧 VBA Automation Scripts

### Historical Data Standardization
**Purpose**: Standardize raw data sheets from 2013-2022 for consistent cell references
**Issue**: Player of the Match field introduction shifted subsequent data down

```vba
Sub ShiftPlayerBlockDown_Corrected()
    Dim ws As Worksheet
    Dim i As Long

    For Each ws In ThisWorkbook.Worksheets
        With ws
            ' Shift values from A14 to A8 (bottom-up to avoid overwriting)
            For i = 15 To 8 Step -1
                .Cells(i, 1).Value = .Cells(i - 1, 1).Value
            Next i
            
            ' Insert label at A7
            .Range("A7").Value = "Player of the Match:"
        End With
    Next ws
End Sub
```

**Application**: Applied to seasons 2013-2022, adding Player of the Match field from end of 2015 onward

## 📋 Sheet Management Strategy

### Team/Opposition Player Sheets
- **Formula-heavy versions**: Dynamic calculations, references to match data
- **Clean versions**: Values only, suitable for team sharing/distribution
- **Dual approach**: Maintains data integrity while enabling external sharing

### Individual Match Sheets
- One sheet per match (`M001_stats`, `M002_stats`, etc.)
- Standardized layout: match info → batting → bowling
- Self-contained for easy reference and validation

## 🔍 Quality Assurance Process
- **Manual checks**: Duplicates, formatting, player aliases
- **Formula audits**: FORMULATEXT() dependency tracing
- **Cross-season validation**: Consistent naming conventions
- **Sheet validation**: Individual match totals vs. aggregated data
- **Historical consistency**: VBA standardization across all pre-2016 seasons
- **Data integrity**: Cell reference validation post-Player of the Match addition

## 📈 Visualization Strategy
- Excel slicers for internal team use
- VBA prototypes for dynamic displays
- Future: Power BI/Streamlit for external dashboards

## 📊 Data Evolution Timeline
- **2013-2015**: Manual data entry, no Player of the Match tracking
- **End 2015**: Player of the Match introduced (sporadic)
- **2016+**: Consistent Player of the Match recording
- **2022+**: Standardized cell structure with Python parser
- **2025**: VBA automation for historical data consistency

---
*Technical documentation by Marcelo*
