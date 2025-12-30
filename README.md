
import React from 'react';
import { AnalysisResult } from '../types';
import { ClipCard } from './ClipCard';
import { Timeline } from './Timeline';
import { Button } from './ui/Button';

interface ClipResultsProps {
  result: AnalysisResult;
}

const formatTime = (seconds: number) => {
  const date = new Date(0);
  date.setSeconds(seconds);
  return date.toISOString().substr(14, 5);
};

export const ClipResults: React.FC<ClipResultsProps> = ({ result }) => {
  const handleDownloadAll = () => {
    // In a real app, this would be a link to a backend endpoint that generates a zip file.
    // For this demo, we'll create a manifest file with info for all clips.
    const allClipsInfo = `
ReplayClipper - All Clips Download
==================================

Video Title: ${result.videoTitle}
Analysis Method: ${result.method}
Total Clips: ${result.clips.length}

---

${result.clips.map((clip, index) => `
Clip ${index + 1}:
  Title: ${clip.title}
  Timestamp: ${formatTime(clip.startTime)} - ${formatTime(clip.endTime)}
  Summary: ${clip.summary}
  Hashtags: ${clip.hashtags.map(h => `#${h}`).join(' ')}
`).join('\n\n---\n\n')}

---
This is a mock download manifest. In a real application, this would be a .zip file containing all video clips.
    `;

    const blob = new Blob([allClipsInfo.trim()], { type: 'text/plain' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = `all-clips-info-${result.videoId}.txt`;
    document.body.appendChild(a);
    a.click();
    document.body.removeChild(a);
    URL.revokeObjectURL(url);
  };

  return (
    <div className="space-y-8 animate-fade-in">
      <div>
        <div className="flex justify-between items-center mb-4">
          <h2 className="text-2xl font-bold">Generated Clips</h2>
          <span className={`px-3 py-1 text-sm font-medium rounded-full ${result.method === 'Heatmap-based' ? 'bg-green-100 text-green-800 dark:bg-green-900 dark:text-green-200' : 'bg-blue-100 text-blue-800 dark:bg-blue-900 dark:text-blue-200'}`}>
            {result.method}
          </span>
        </div>
        <p className="text-gray-600 dark:text-gray-400 mb-2">
          Video: <span className="font-medium">{result.videoTitle}</span>
        </p>
        <Timeline clips={result.clips} duration={result.duration} />
      </div>

      <div className="flex justify-end">
        <Button onClick={handleDownloadAll}>
          Download All (.zip)
        </Button>
      </div>

      <div className="grid grid-cols-1 gap-6">
        {result.clips.map((clip) => (
          <ClipCard key={clip.id} clip={clip} />
        ))}
      </div>
    </div>
  );
};
