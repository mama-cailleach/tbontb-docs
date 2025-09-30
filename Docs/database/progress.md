# 🗂️ Cricket Data Workflow – Progress Log

## 📅 Current Status (August 2025)
- **Raw Data Collection**: ✅ Seasons 2013-2025 (13 seasons complete)
- **Python Parser**: ✅ Enhanced with Player of the Match support
- **Season Outputs**: ✅ Generated for all seasons 2013-2025
- **Data Standardization**: ✅ Completed across all historical seasons
- **VBA Automation**: ✅ Historical data correction + opposition extras scripts
- **Phase 3 Consolidation**: ✅ Master data + player analytics complete
- **Phase 4 Development**: 🔄 Dashboard prototyping + reporting framework active

---

## 🔧 Workflow Evolution
**Phase 1**: Raw match data → Python processing → Season summaries ✅  
**Phase 2**: VBA standardization → Data validation → Historical consistency ✅  
**Phase 3**: Data quality assurance → Master workbooks → Player analytics ✅  
**Phase 4**: Dashboard prototyping → Reporting framework → Team distribution 🔄  

## 📊 Major Achievements (Recent)

### ✅ **Data Collection Milestone (2025)**
- **Raw data scraped**: All seasons 2013-2025 stored in individual workbooks
- **Season 2025**: Latest season completed and processed
- **Season outputs**: Complete `season_summary`, `all_batting`, `all_bowling` for 13 seasons
- **File organization**: Structured in `spreadsheets/raw data and outputs/` folder
- **Data volume**: 13 years of match statistics now digitized

### ✅ **Python Parser Enhancements**
- **Player of the Match**: Added extraction and storage capabilities
- **Extras handling**: Moved from batting to bowling scorecard (more cricket-accurate)
- **Extras bug fixes**: Corrected swapped runs_conceded assignment and negative value handling
- **Data structure**: Enhanced vertical match info layout
- **Error handling**: Improved robustness for multi-season processing
- **Opposition naming**: Fixed dynamic Team/Opposition Extras labeling

### ✅ **Data Quality & Corrections (Completed)**
- **Cell reference fix**: Player of the Match addition shifted team name location
- **Feature timeline**: Player of the Match introduced end of 2015, consistent from 2016
- **Seasons corrected**: 2017-2025 ✅ (All affected seasons validated and fixed)
- **Historical standardization**: 2013-2016 re-parsed with current parser standards ✅
- **VBA automation**: Created script to standardize raw data sheets (2013-2022)
- **Player of the Match backfill**: Added to all seasons from end of 2015 onward

### ✅ **VBA Data Standardization (Completed)**
- **Historical data automation**: VBA macro for standardizing raw data sheets
- **Opposition extras renaming**: Automated cleanup across all season workbooks
- **Extras bug fixes**: Swapped extras runs_conceded corrected across all matches
- **Batch processing**: Applied to seasons 2013-2022 for consistent formatting
- **Cell reference alignment**: Ensured all seasons use consistent data structure
- **Quality improvement**: Eliminated manual data correction across 11 seasons

**VBA Opposition Extras Script:**
```vba
Sub RenameOppositionExtras()
    Dim ws As Worksheet, lastRow As Long, i As Long
    Dim idCol As String: idCol = "D" ' player_id column
    Dim nameCol As String: nameCol = "E" ' name column
    
    For Each ws In ThisWorkbook.Worksheets
        lastRow = ws.Cells(ws.Rows.Count, idCol).End(xlUp).Row
        For i = 2 To lastRow
            If ws.Cells(i, idCol).Value Like "O###_EXTRAS" And _
               ws.Cells(i, idCol).Value <> "TBONTB_EXTRAS" Then
                ws.Cells(i, nameCol).Value = "Opposition Extras"
            End If
        Next i
    Next ws
End Sub
```

**VBA Extras Swap Fix Script:**
```vba
Sub SwapExtrasRunsConceded()
    Dim ws As Worksheet
    Set ws = ThisWorkbook.Sheets("all_bowling")
    Dim lastRow As Long
    lastRow = ws.Cells(ws.Rows.Count, "A").End(xlUp).Row
    
    Dim matchDict As Object
    Set matchDict = CreateObject("Scripting.Dictionary")
    Dim innerDict As Object
    
    ' Find Team/Opposition Extras rows by match
    For i = 2 To lastRow
        Dim matchID As String, playerName As String
        matchID = ws.Cells(i, 1).Value
        playerName = ws.Cells(i, 5).Value
        
        If playerName = "Team Extras" Or playerName = "Opposition Extras" Then
            If Not matchDict.exists(matchID) Then
                Set innerDict = CreateObject("Scripting.Dictionary")
                matchDict.Add matchID, innerDict
            Else
                Set innerDict = matchDict(matchID)
            End If
            innerDict(playerName) = i
        End If
    Next i
    
    ' Swap runs_conceded values
    For Each mID In matchDict.Keys
        Set innerDict = matchDict(mID)
        If innerDict.exists("Team Extras") And innerDict.exists("Opposition Extras") Then
            Dim teamRow As Long, oppRow As Long
            teamRow = innerDict("Team Extras")
            oppRow = innerDict("Opposition Extras")
            
            Dim teamRuns As Variant, oppRuns As Variant
            teamRuns = ws.Cells(teamRow, 7).Value
            oppRuns = ws.Cells(oppRow, 7).Value
            
            ws.Cells(teamRow, 7).Value = oppRuns
            ws.Cells(oppRow, 7).Value = teamRuns
        End If
    Next mID
End Sub
```

### ✅ **Phase 3: Data Consolidation & Player Analytics (Completed)**
- **Master data consolidation**: `all_seasons_master.xlsx` with stacked batting/bowling data
- **Player-centric analytics**: `TBONTB_players.xlsx` with comprehensive player statistics
- **Power Query automation**: Dynamic player summaries with batting/bowling aggregations
- **Cricket-specific calculations**: Base-5 overs handling, proper BBI logic, career statistics
- **ID registry system**: Sequential player IDs based on debut appearance
- **Team performance tracking**: Cross-season analysis with calculated metrics (avg, strike rate, economy)
- **Opposition data**: Master consolidation complete (detailed analytics pending Phase 4)

### ✅ **Data Quality Assurance & Bug Fixes (August 2025)**
- **Extras swap investigation**: Identified and confirmed swapped extras across all seasons
- **Python parser fixes**: Corrected extras assignment logic for future parsing
- **VBA remediation**: Applied extras swap fix to master workbooks
- **Data integrity audit**: Cross-referenced player stats against original database
- **Duplicate match correction**: Fixed 2025 scorecard duplication issue
- **Missing match recovery**: Found and added hidden 2018 tied match from website
- **Match ID restructuring**: Shifted all 2019+ match IDs to accommodate 2018 addition
- **Version control**: Maintained backup copies with changelog documentation
- **QA validation**: Comprehensive sanity checks on totals and player aggregates


### ✅ September 2025: Venue-Based Reporting & Data Workflow Enhancement

- **Venue reporting process launched:** Created a dedicated `TBONTB_venues.xlsx` workbook, separate from the master stats file, to support venue-focused reports and future deep dives.
- **Pivot table-driven stats extraction:** Used pivot tables to separate, clean, and organize venue stats for easy access and reporting.
- **Reusable analysis:** The new workbook structure allows for quick updates and targeted venue analysis without impacting the main master stats file.

### ✅ August 2025: Season-by-Season Reporting, QA, and Data Insights
- **Comprehensive season-by-season tables completed:** Populated all summary and milestone tables in `report_2.md` for 2013–2025 using a mix of manual entry, AI tools, and Excel dashboards.
- **Workflow enhancement:** Leveraged the `TBONTB_master_dashboard.xlsx` with formulas, dropdowns, and pivot tables to streamline data extraction and reporting. Used helper columns for opposition debuts and Player of the Match stats.
- **Data QA and anomaly detection:** While compiling tables, uncovered and documented new data inconsistencies (e.g., negative wickets in 2016, negative extras, and misattributed wickets in specific matches). These findings are now recorded for future correction and serve as valuable trivia.
- **Documentation and reporting process improved:** Established a repeatable workflow for updating season-by-season stats, including cross-checking with source data and using modular report outlines for clarity and extensibility.

### 🔄 **Phase 4: Dashboard & Reporting System (In Progress)**
- **Excel dashboard prototype**: Modular interface with dropdown season selection
- **Dynamic visualization**: All seasons vs. individual season metrics switching
- **Markdown reporting framework**: YAML metadata structure for versatile output formats
- **First comprehensive report**: 13-season statistical analysis (181 matches, 2013-2025)
- **Analytical metrics development**: NRR calculations, dismissal breakdowns, milestone tracking
- **Average game score concept**: Experimental metric for team performance visualization
- **Static website preparation**: GitHub Pages integration potential for public sharing

## 📝 Current Focus (Phase 4)
1. **Visualization & Reporting**: Dashboard prototyping and analytics presentation
   - Excel modular dashboard with season selection capabilities ✅
   - Markdown reporting system with YAML metadata structure ✅
   - Comprehensive statistical analysis across all seasons ✅
   - Static website exploration for public team sharing 🔄
2. **Season Workbook Standardization**: Template-based display structure 🔄
   - Review Season 2013 display format for improvements
   - Create reusable match summary block template
   - VBA automation for formula ranges and formatting
   - Standardized player performance highlight sections

## 📝 Phase 4 Goals (Team Distribution & Visualization)
- **Dashboard development**: Interactive Excel prototypes → web-based analytics
- **Reporting framework**: Markdown-based statistical reports with YAML metadata
- **Season workbook templates**: Standardized formula-rich analysis workbooks
- **Plain data sharing**: CSV exports and clean data versions for analysis
- **Static website**: GitHub Pages integration for public team statistics
- **Opposition analytics**: Detailed opponent performance tracking
- **VBA automation**: Workbook updates and dashboard maintenance

## 📈 Technical Debt & Improvements
- ~~**Historical consistency**: Ensuring all seasons use same data model~~ ✅ 
- ~~**Quality assurance**: Systematic validation across 13 seasons~~ ✅
- ~~**Master data consolidation**: Central workbooks for analysis~~ ✅
- ~~**Player statistics engine**: Automated career stats and season highlights~~ ✅
- ~~**Data integrity issues**: Extras swap bug and missing match corrections~~ ✅
- **Opposition intelligence**: Detailed opponent performance tracking (Phase 4)
- **Season workbook templates**: Standardized formula-rich analysis workbooks
- **Data sharing**: Plain data versions for external collaboration
- **No Result policy**: Matches retained in fixture lists but excluded from statistical summaries
- **Season outputs accuracy**: Some legacy season_output files contain swapped extras (noted for future reference)

---

## 🧠 Development Notes
- **Repository**: Now managed on GitHub (mama-cailleach/TBONTB)
- **Data pipeline**: Raw → Parser → Season Output → Master Consolidation → Analytics → Reporting ✅
- **Workbook strategy**: Power Query + Excel formulas for dynamic player statistics
- **Phase 3 achievement**: Comprehensive team player analytics system operational
- **Phase 4 approach**: Dual-track development (Excel dashboards + Markdown reports)
- **Quality methodology**: Multi-layered validation with VBA automation and cross-referencing
- **Version control**: Backup strategies with pre-fix snapshots (e.g., pre_2018_fix)
- **Reporting strategy**: YAML-structured markdown for flexible output (GitHub Pages potential)
- **Future integrations**: Power BI/Tableau experiments planned for Phase 5+
- **Team sharing**: Balance between interactive dashboards and static report sharing

---

## 📑 Reporting & Publishing (August 2025)
- **Markdown reports framework**: Developed a modular system for writing and organizing statistical reports
- **Jekyll + GitHub Pages**: Learned and implemented a simple blog to host TBONTB stats online
- **Seamless sharing**: Reports are now easily accessible on both mobile and PC by adding markdown files to the correct folder
- **Future design improvements**: Will continue to explore Jekyll for enhanced blog layout and features
