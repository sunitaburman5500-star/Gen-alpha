# Gen-alpha
import { useEffect, useRef, useState } from "react";
import html2pdf from "html2pdf.js";
import { Card, CardContent } from "@/components/ui/card";
import { Tabs, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { Button } from "@/components/ui/button";

const subjects = [/* same as before */];
const levels = [/* same as before */];
const characterImages = {
  "Professor Owl": "/images/professor-owl.png",
  "SuperNova": "/images/supernova.png",
  "Miss Chalk": "/images/miss-chalk.png",
  "Captain Bright": "/images/captain-bright.png",
};

export default function LearningApp() {
  const [selectedCharacter, setSelectedCharacter] = useState("Professor Owl");
  const [selectedSubject, setSelectedSubject] = useState(subjects[0]);
  const [selectedLevel, setSelectedLevel] = useState(levels[0]);

  const greenBoardRef = useRef();

  // 🧠 Load previous session from localStorage
  useEffect(() => {
    const savedLevel = localStorage.getItem("selectedLevel");
    const savedSubject = localStorage.getItem("selectedSubject");
    const savedCharacter = localStorage.getItem("selectedCharacter");

    if (savedLevel) setSelectedLevel(savedLevel);
    if (savedSubject) setSelectedSubject(savedSubject);
    if (savedCharacter) setSelectedCharacter(savedCharacter);
  }, []);

  // 💾 Save whenever any selection changes
  useEffect(() => {
    localStorage.setItem("selectedLevel", selectedLevel);
  }, [selectedLevel]);

  useEffect(() => {
    localStorage.setItem("selectedSubject", selectedSubject);
  }, [selectedSubject]);

  useEffect(() => {
    localStorage.setItem("selectedCharacter", selectedCharacter);
  }, [selectedCharacter]);

  const story = `Once upon a time, ${selectedCharacter} walked into ${selectedLevel} with a glowing green board. Today, we're learning "${selectedSubject}" through stories!`;

  const handleDownloadPDF = () => {
    const element = greenBoardRef.current;

    const options = {
      margin: 0.5,
      filename: `${selectedSubject}_${selectedLevel}.pdf`,
      image: { type: 'jpeg', quality: 0.98 },
      html2canvas: { scale: 2 },
      jsPDF: { unit: 'in', format: 'letter', orientation: 'portrait' },
    };

    html2pdf().set(options).from(element).save();
  };

  return (
    <div className="p-6 space-y-6">
      <h1 className="text-3xl font-bold text-center">GreenBoard Stories</h1>

      <Card>
        <CardContent className="p-4 space-y-4">
          {/* Class Tabs */}
          <div>
            <label className="block mb-1 font-medium">Choose Your Class:</label>
            <Tabs defaultValue={selectedLevel} onValueChange={setSelectedLevel}>
              <TabsList className="grid grid-cols-4 gap-1">
                {levels.map(level => (
                  <TabsTrigger key={level} value={level}>
                    {level}
                  </TabsTrigger>
                ))}
              </TabsList>
            </Tabs>
          </div>

          {/* Subject Selector */}
          <div>
            <label className="block mb-1 font-medium">Choose Subject:</label>
            <select
              className="w-full p-2 border rounded"
              value={selectedSubject}
              onChange={e => setSelectedSubject(e.target.value)}
            >
              {subjects.map(subject => (
                <option key={subject}>{subject}</option>
              ))}
            </select>
          </div>

          {/* Character Selector */}
          <div>
            <label className="block mb-1 font-medium">Choose Teaching Character:</label>
            <select
              className="w-full p-2 border rounded"
              value={selectedCharacter}
              onChange={e => setSelectedCharacter(e.target.value)}
            >
              {Object.keys(characterImages).map(character => (
                <option key={character}>{character}</option>
              ))}
            </select>
          </div>
        </CardContent>
      </Card>

      {/* Character Image */}
      <div className="text-center">
        <img
          src={characterImages[selectedCharacter]}
          alt={selectedCharacter}
          className="mx-auto h-32"
        />
      </div>

      {/* GreenBoard */}
      <div
        ref={greenBoardRef}
        className="bg-green-900 text-white p-6 rounded-xl font-mono shadow-lg min-h-[150px]"
      >
        {story}
      </div>

      {/* Download Button */}
      <div className="text-center">
        <Button onClick={handleDownloadPDF} className="mt-4">
          Download as PDF
        </Button>
      </div>
    </div>
  );
}
