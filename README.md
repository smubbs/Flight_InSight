# Flight InSight

**Flight InSight** is an AI-powered flight dispatch system that provides comprehensive pre-flight briefings to pilots. The system uses specialized AI agents to analyze weather conditions, NOTAMs (Notices to Airmen), and historical performance data to deliver professional, concise, and temporally relevant flight briefings.

## See the Demo!
https://www.figma.com/make/MHq1yN31DM2d41qt1DCork/Flight-InSight?t=ma6rIRIEvIMwbJBa&fullscreen=1
Figma account required.

## Overview

Flight InSight coordinates multiple specialized AI agents to generate comprehensive flight briefings:

- **Dispatcher Agent**: The main orchestrator that produces final briefings for pilots
- **Weather Agent**: Retrieves and filters aviation weather (METARs, TAFs, SIGMETs, winds aloft)
- **NOTAM Agent**: Provides relevant NOTAMs for departure, destination, and alternate airports
- **Efficiency Agent**: Analyzes historical operational data (up to 120 days) for performance insights
- **Maintenance Agent**: Provides technical status of the aircraft, including defects and operational impact.

The system ensures all data is temporally filtered based on the flight's ETD (Estimated Time of Departure) and ETA (Estimated Time of Arrival), providing only relevant and actionable information to pilots.

## Architecture

```
┌─────────────────────┐
│  Dispatcher Agent   │
│   (Orchestrator)    │
└──────────┬──────────┘
           │
           ├───────────────┐
           │               │
    ┌──────▼──────┐  ┌────▼────────┐
    │   Weather   │  │    NOTAM    │
    │    Agent    │  │    Agent    │
    └──────┬──────┘  └────┬────────┘
           │               │
           └───────┬───────┘
                   │
            ┌──────▼──────────┐
            │   Efficiency    │
            │     Agent       │
            └─────────────────┘
```

All agents output structured JSON data that feeds into the Dispatcher Agent's final briefing.

## Directory Structure

```
Flight_InSight/
├── flight_plans/           # Sample flight data and operational packages
│   ├── CX739/             # Flight CX739 data
│   └── CX840/             # Flight CX840 data (Hong Kong → New York JFK)
│       ├── flight_plan.json      # Detailed flight information
│       ├── weather_package.txt   # Weather data
│       ├── notams.txt           # NOTAM package
│       ├── efficiency.json      # Historical performance data
│       └── aircraft_status.json # Aircraft status information
├── prompts/               # AI agent prompt templates
│   ├── dispatcher.prompt.yaml   # Main dispatcher agent prompt
│   ├── wx.prompt.yaml          # Weather agent prompt
│   ├── notam.prompt.yaml       # NOTAM agent prompt
│   ├── efficiency.prompt.yaml  # Efficiency agent prompt
│   ├── eng.prompt.yaml         # Engineering agent prompt
│   └── json/                   # Auto-generated JSON versions of prompts
├── n8n overview/          # System architecture documentation
│   └── Dispatch Agent AI Overview.png
└── .github/workflows/     # CI/CD automation
    └── convert-prompts.yaml    # Auto-converts YAML prompts to JSON
```

## Features

### Dispatcher Agent
- Generates structured flight briefings with five sections:
  - **Introduction**: Flight overview and greeting
  - **Aircraft Status**: Aircraft-specific information and efficiency data
  - **Departure**: Departure airport conditions and NOTAMs
  - **Enroute**: Weather, turbulence, and routing information
  - **Arrival**: Destination conditions, approach information, and parking

### Weather Agent
- Retrieves METARs, TAFs, and SIGMETs
- Filters weather data based on flight timing
- Identifies significant weather challenges (visibility, cloud base, hazards)
- Provides enroute turbulence forecasts

### NOTAM Agent
- Filters NOTAMs by temporal relevance
- Covers departure, destination, and alternate airports
- Summarizes critical operational impacts

### Efficiency Agent
- Analyzes historical performance (up to 120 days)
- Provides block time, fuel burn, and payload trends
- Identifies potential FIR shortcuts and their impacts
- Highlights efficiency opportunities

## Prompt Management

The project uses YAML files to define AI agent prompts, which are automatically converted to JSON format via GitHub Actions when changes are pushed.

### Prompt Structure
Each prompt YAML file includes:
- **Metadata**: ID, name, description, author, tags
- **Context**: Role, domain, and constraints
- **Inputs**: Required and optional input parameters
- **Outputs**: Expected JSON schema
- **Content**: The actual prompt text with placeholders

### Automatic Conversion
When you modify any `.prompt.yaml` file in the `prompts/` directory, the GitHub Actions workflow automatically:
1. Detects the change
2. Converts the YAML to JSON using `yq`
3. Saves the JSON version to `prompts/json/`
4. Commits and pushes the changes

## Sample Data

The repository includes sample flight data for demonstration purposes:

### CX840 (Hong Kong → New York JFK)
- **Aircraft**: Airbus A350-900
- **Route**: VHHH → East Asia → NOPAC/PACOTS → Arctic → NAT Track → KJFK
- **Block Time**: 15:25
- **ETOPS**: 330 minutes with alternates KEWR, KBOS
- **Unique Features**:
  - Trans-polar routing
  - Multiple oceanic crossings (Pacific, Arctic, Atlantic)
  - Comprehensive FIR shortcut opportunities

## Usage

### Prerequisites
- AI agent platform (e.g., n8n, LangChain, or custom orchestrator)
- Access to aviation data sources (weather, NOTAMs, performance databases)

### Integration
1. Load the prompt templates from the `prompts/json/` directory
2. Configure your AI agent platform to use these prompts
3. Provide the required input data (flight information, weather, NOTAMs, efficiency data)
4. The Dispatcher Agent will orchestrate calls to specialized agents and generate the final briefing

### Example Output
```json
{
  "Introduction": "Good morning! Today you are flying to New York JFK. Here's your briefing for the flight.",
  "Aircraft Status": "Dispatch has added 1.5 tonnes of extra fuel. Historical data shows this route averages 15.5 hours block time.",
  "Departure": "Hong Kong weather is clear with light easterly winds. Runway 25L departure expected. Note: TWY F between E2 and D is closed due to work in progress.",
  "Enroute": "Westerly jet stream up to 150 knots at FL350. Moderate CAT forecast near Aleutians. FIR shortcuts available in Anchorage and Gander oceanic areas could save up to 0.2 hours.",
  "Arrival": "New York JFK weather shows northwest winds 12-18 knots with broken clouds at 5000 feet. Be aware of laser activity hazard on final approach corridors. ILS RWY 31L undergoing periodic maintenance."
}
```

## Development

### Modifying Prompts
1. Edit the `.prompt.yaml` files in the `prompts/` directory
2. Commit and push your changes
3. GitHub Actions will automatically generate the JSON versions
4. Use the JSON files in your AI agent platform

### Adding New Flight Data
Create a new directory under `flight_plans/` with:
- `flight_plan.json`: Flight details
- `weather_package.txt`: Weather information
- `notams.txt`: Relevant NOTAMs
- `efficiency.json`: Historical performance data (optional)
- `aircraft_status.json`: Aircraft-specific information (optional)

## Contributing

Contributions are welcome! Please ensure:
- Prompt templates follow the established YAML structure
- Sample data is realistic and properly formatted
- Documentation is updated for new features

## License

This project is provided as-is for aviation operations and AI agent development purposes.

## Acknowledgments

Built with expertise in aviation dispatch operations and modern AI agent architectures.

---

**Note**: This system is designed for demonstration and development purposes. Always verify critical flight information through official aviation sources and certified dispatch systems.
