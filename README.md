# Mirth + DICOM: Receive, Transform & Send (Orthanc round-trip)

This repository contains a working solution for a DICOM round-trip using Mirth Connect 4.5.0: receive studies via DIMSE, persist them to disk, slightly modify patient metadata, and send them on to an Orthanc server. It also includes the original assignment (“problem paper”) and our short solution report.

## Repository structure
- channels/dicom-receiver-from-Orthanc.xml – DICOM Listener (AE = MIRTH) on port 104 that writes incoming files to ./mirth_data/in/dicom/${message.id}.dcm. 

- channels/dicom-sender-toOrthanc.xml – File Reader polling ./mirth_data/in/dicom/*.dcm, updates a few patient tags, and sends to Orthanc (host=orthanc, port=9112, remote AE=EHEALTH, local AE=MIRTH) via a DICOM Sender. 

- docs/eHealth … Aufgaben.pdf – Assignment handout with pointers (Orthanc docs, sample DICOMs, tools). 

- docs/Report.pdf – Short solution notes & screenshots (C-FIND/C-MOVE success, etc.). 

## Prerequisites
- Mirth Connect 4.5.0 (channels were exported with 4.5.0). 
- A reachable Orthanc DICOM server (AE EHEALTH) at orthanc:9112 or adjust the channel config accordingly. 
- Optional viewers/tools referenced in the assignment (sample DICOMs, editors, etc.). 

> ⚠️ Port 104 is privileged on some OSes; run Mirth with sufficient privileges or change the listener port in the channel if needed. (General ops note.)

## Quick start
1. Create the working directories:
> mkdir -p ./mirth_data/in/dicom ./mirth_data/temp/dicom
2. Import the channels in Mirth:
- Channels → Import from file… and select both XMLs from channels/.

3. Verify / adjust endpoints:
- In dicom-receiver-from-Orthanc: confirm Listener port (default 104) and AE MIRTH. 
- In dicom-sender-toOrthanc: confirm File Reader path ./mirth_data/in/dicom and DICOM Sender target orthanc:9112 (AE=EHEALTH). 
4. Deploy both channels.
5. Test the flow:
- Push a DICOM to the receiver (C-STORE to AE=MIRTH, port=104). The file should appear under ./mirth_data/in/dicom. 
- The sender will pick it up, update patient tags (see below), move the original to ./mirth_data/temp/dicom, and send to Orthanc. 
- Use your PACS/Viewer to C-FIND/C-MOVE as in the handout; our report documents successful queries/moves.