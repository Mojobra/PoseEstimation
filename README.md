# Pose Estimation

This project uses MediaPipe Pose to analyze side-view squat videos and generate movement feedback from 2D joint angles. The main workflow lives in the notebook [PoseEstimation.ipynb](/C:/Users/mauri/Documents/New%20project/PoseEstimation.ipynb).

The pipeline processes smartphone videos frame by frame, estimates pose landmarks, computes joint angles, smooths noisy signals, detects squat repetitions, and exports both tabular results and annotated videos.

## What the project does

- Extracts 2D pose landmarks from side-view `.mp4` videos using MediaPipe Pose
- Selects the visible body side automatically
- Computes squat-related joint angles such as knee, hip, and ankle angles
- Smooths angle trajectories with a One Euro filter
- Interpolates short gaps caused by low-visibility frames
- Segments squat repetitions from the angle time series
- Generates rule-based feedback for each repetition
- Exports CSV summaries and annotated videos

## Project structure

- [PoseEstimation.ipynb](/C:/Users/mauri/Documents/New%20project/PoseEstimation.ipynb): main notebook containing the full pipeline
- [README.md](/C:/Users/mauri/Documents/New%20project/README.md): project documentation

## How the pipeline works

1. A side-view squat video is loaded with OpenCV.
2. MediaPipe Pose detects body landmarks for each frame.
3. Landmarks are converted from normalized coordinates to pixel coordinates.
4. The notebook determines whether the left or right side should be used for analysis.
5. Joint angle signals are filtered and cleaned.
6. Squat repetitions are segmented from the knee-angle time series.
7. Each repetition is scored with rule-based feedback.
8. Results are written to CSV files and annotated videos.

## Feedback generated

The notebook labels repetitions with flags such as:

- `LOW_CONF`: tracking confidence was too low for a reliable rep
- `SHALLOW`: squat depth appears too shallow
- `ANKLE_LIMIT`: possible limited ankle dorsiflexion
- `HIP_DOM`: hip/trunk-dominant squat strategy
- `KNEE_EXT`: excessive knee extension

The exact thresholds are defined inside the notebook and can be tuned there.

## Requirements

Recommended environment:

- Python 3.10 or newer
- Jupyter Notebook or JupyterLab

This project now includes a dependency file at [requirements.txt](/C:/Users/mauri/Documents/New%20project/requirements.txt).

Install the dependencies with:

```bash
pip install -r requirements.txt
```

If you prefer using a virtual environment:

```bash
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
```

## Running the project

1. Start Jupyter:

```bash
jupyter notebook
```

2. Open [PoseEstimation.ipynb](/C:/Users/mauri/Documents/New%20project/PoseEstimation.ipynb).

3. Run the notebook cells in order.

4. In the final configuration section of the notebook, set:

- `video_directory` to the folder containing your input `.mp4` videos
- `output_dir` to the folder where results should be saved
- `model_complexity` to the MediaPipe model level you want to use

The notebook then loops through all `.mp4` files in `video_directory` and processes them one by one.

## Key parameters

The main processing function inside the notebook is `process_video(...)` with these important settings:

- `model_complexity=2`: highest accuracy, slower runtime
- `detection_confidence=0.7`: minimum detection confidence
- `tracking_confidence=0.7`: minimum tracking confidence
- `visibility_gate=0.6`: filters low-visibility landmark frames
- `sample_frames_for_side=30`: number of frames used to determine body side

The One Euro smoothing filter is also configurable through:

- `min_cutoff`
- `beta`
- `derivative_cutoff`

## Outputs

For each input video, the notebook produces:

- A frame-level CSV file with pose and analysis data
- A repetition summary CSV with one row per detected squat rep
- An annotated video with the pose overlay
- A feedback video with rep flags shown on screen

Typical filenames look like:

- `videoName_2.csv`
- `videoName_2_rep_summary.csv`
- `videoName_2.mp4`
- `videoName_2_feedback.mp4`

## Expected input

Best results will usually come from videos that are:

- recorded from the side view
- focused on a single person
- well lit
- stable, with limited camera motion
- clear enough for hip, knee, ankle, and shoulder landmarks to remain visible

## Limitations

- The approach is based on 2D pose estimation, not full 3D biomechanics
- It is designed around side-view squat analysis and may not generalize well to other camera angles
- Occlusion, blur, loose framing, or poor lighting can reduce accuracy
- Feedback is heuristic and should be treated as supportive analysis, not clinical diagnosis

## Notes

- The notebook currently references a local variable named `DATENAUFNAHME` in the final batch-processing section. If that variable is not defined in your environment, replace it with a direct folder path string.
- Output folders are created automatically when needed.
- The code prefers H.264 output for the feedback video and falls back to `mp4v` if necessary.

## Possible next improvements

- Convert the notebook pipeline into a reusable Python script or package
- Add sample input/output data
- Add plots for angle curves over time
- Add tests for helper functions and rep segmentation logic

## References

- [MediaPipe Pose documentation](https://chuoling.github.io/mediapipe/solutions/pose.html)
- [MediaPipe GitHub repository](https://github.com/google-ai-edge/mediapipe)
- [OpenCV video I/O tutorial](https://docs.opencv.org/3.4/dd/d43/tutorial_py_video_display.html)
- [One Euro Filter reference](https://gery.casiez.net/1euro/)
