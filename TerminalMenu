//==============================================|
//Aperture Sciecne Terminal Chambers v0.2
//==============================================|
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Threading;
using System.IO;

namespace APTC
{
    class TerminalMenu
    {
        #region constant
        const string emptyChamberName = "none";
        #endregion

        private const int MaxSubject = 10;
        public static int pauseTime = 1000;
        private const ConsoleColor textColor = ConsoleColor.White,
                                   textBg = ConsoleColor.Black,
                                   sectionTextColor = ConsoleColor.Black,
                                   sectionBgColor = ConsoleColor.White,
                                   okStatusColor = ConsoleColor.Green,
                                   badStatusColor = ConsoleColor.Red;

        private Dictionary<int, Bot> Chambers = new Dictionary<int, Bot>(MaxSubject);
        private Dictionary<string, Action> Commands = new Dictionary<string, Action>();

        private bool closeMenu = false;
        public TerminalMenu()
        {
            Commands.Add("help", Help);
            Commands.Add("chambers", ChambersList);
            Commands.Add("control", Control);
            Commands.Add("subjects", Subjects);
            Commands.Add("reset", Reset);

            Commands.Add("quit", Quit);

            //Chambers.Add(1, new Bot("flex"));
            //Chambers.Add(2, new Bot("alex"));

            string[] allFoundFiles = Directory.GetFiles(Directory.GetCurrentDirectory() + "\\Subjects").Select(Path.GetFileName).ToArray();
            for (int i = 1; i <= MaxSubject; i++)
            {
                Chambers.Add(i, new Bot(emptyChamberName, i));
                if (allFoundFiles.Contains(Convert.ToString(i)))
                    Chambers[i].Load();
                //else
                //    Chambers[i].Save();                      
            }
            MainMenu();
        }

        private void MainMenu()
        {
            string inputCommand;
            do
            {
                ConsoleReset();
                GeneralInformation();

                inputCommand = Console.ReadLine().ToLower();

                if (Commands.ContainsKey(inputCommand))
                {
                    Console.Clear();
                    Commands[inputCommand]();
                }
                else
                {
                    ConsoleCommandReport("Bad command: " + inputCommand, false);
                    Console.ReadKey();
                }
            } while (!closeMenu);
        }
        private void GeneralInformation()
        {
            Console.Clear();
            //Ttile
            ConsoleInterfaceSelection("Aperture Sciecne Terminal Chambers v0.2", '/');

            //Info
            ConsoleInterfaceSelection("Information", '-');
            Console.Write("Chambers: " + Chambers.Count + "\nLife status: ");
            ConsoleStatusDisplay(!Chambers.Any(x => !x.Value.GetLifeStatus), "OK", "ERROR");
            Console.Write("\n\nActive: " + Chambers.Count(x => x.Value.GetThreadStatus)  + '\n');

            //Menu
            ConsoleInterfaceSelection("Terminal Menu", '-');
            Console.Write(">:");
        }

        #region Commands
        private void Subjects()
        {
            Console.Clear();
            ConsoleInterfaceSelection("Subjects control", '=');
            Console.Write("Select id: ");
            int id;
            Int32.TryParse(Console.ReadLine(), out id);
            if (Chambers.ContainsKey(id))
            {
                if (Chambers[id].GetName == emptyChamberName)
                {
                    Console.Write("\nName: ");
                    string enterName = Console.ReadLine();
                    if (enterName == emptyChamberName)
                        ConsoleCommandReport("Forbidden name!", false);
                    else
                    {
                        Bot newSubject = new Bot(enterName, id);
                        Chambers[id] = newSubject;
                        ConsoleCommandReport("Subject added", true);
                        Chambers[id].Save();
                    }
                }
                else
                    ConsoleCommandReport("Subject already added!", false);
            }
            else
                ConsoleCommandReport("Wrong id!", false);
            Console.ReadKey();
        }
        private void Help()
        {
            ConsoleInterfaceSelection("General commands", '=');
            foreach (string command in Commands.Keys)
                Console.WriteLine("# " + command);
            Console.ReadKey();

        }
        private void ChambersList()
        {
            ConsoleInterfaceSelection("Chambers list", '=');
            int id = 1;
            foreach (Bot chamber in Chambers.Values)
            {
                Console.WriteLine("Chamber №" + id);
                if (chamber.GetName != emptyChamberName)
                {
                    Console.Write("Name: " + chamber.GetName +
                  "\nStatus: ");
                    ConsoleStatusDisplay(chamber.GetLifeStatus, "Alive", "Dead");
                    Console.Write("\nMode: ");
                    ConsoleStatusDisplay(chamber.GetThreadStatus, "ON", "OFF");
                    Console.WriteLine("\n");
                }
                else
                {
                    Console.ForegroundColor = badStatusColor;
                    Console.WriteLine("\n Empty\n");
                    ConsoleReset();
                }
                id++;
                Thread.Sleep(500);
            }
            Console.ReadKey();
        }
        private void Control()
        {
            ConsoleInterfaceSelection("Chambers control", '=');
            Console.Write("Select id: ");
            int id;
            Int32.TryParse(Console.ReadLine(), out id);
            if (Chambers.ContainsKey(id))
            {
                if (Chambers[id].GetName != emptyChamberName)
                {
                    Dictionary<ConsoleKey, Action> KeyControl = new Dictionary<ConsoleKey, Action>();
                    KeyControl.Add(ConsoleKey.A, Chambers[id].GetActivateChamber);
                    KeyControl.Add(ConsoleKey.D, Chambers[id].GetDeactivateChamber);
                    KeyControl.Add(ConsoleKey.K, Chambers[id].GetKillSubject);
                    KeyControl.Add(ConsoleKey.S, Chambers[id].GetSave);
                    Thread displayThread = new Thread(delegate ()
                    {
                        while (true)
                        {
                            Console.Clear();
                            Console.Write("\nName: " + Chambers[id].GetName +
                                "\nStatus: ");
                            ConsoleStatusDisplay(Chambers[id].GetLifeStatus, "Alive", "Dead");
                            Console.Write("\nMode: ");
                            ConsoleStatusDisplay(Chambers[id].GetThreadStatus, "ON", "OFF");

                            ConsoleInterfaceSelection("Indicators:", '-');
                            Chambers[id].DisplayStat();

                            ConsoleInterfaceSelection("Keys:", '-');
                            Console.WriteLine(((Chambers[id].GetThreadStatus) ? "\nD - Deactivate" : "\nA - Activate") + "\nEsc - Exit\nS - Save");
                            Thread.Sleep(pauseTime);
                        }
                    });
                    displayThread.IsBackground = true;
                    displayThread.Start();
                    do
                    {
                        ConsoleKey inputKey = Console.ReadKey().Key;
                        if (KeyControl.ContainsKey(inputKey))
                        {
                            if (Chambers[id].GetLifeStatus)
                                Chambers[id].ExecuteCommand(KeyControl[inputKey]);
                            else
                            {
                                displayThread.Abort();
                                ConsoleCommandReport(Chambers[id].GetName + " is dead!", false);
                                break;
                            }
                        }
                        else if (inputKey == ConsoleKey.Escape)
                            break;
                    } while (true);
                    displayThread.Abort();
                }
                else
                {
                    ConsoleCommandReport("Chamber is empty!", false);
                    Console.ReadKey();
                }
            }
            else
            {
                ConsoleCommandReport("Wrong id!", false);
                Console.ReadKey();
            }

        }
        private void Quit()
        {
            closeMenu = true;
        }
        private void Reset()
        {
            ConsoleInterfaceSelection("Reset chambers", '=');
            Console.WriteLine("Subjects: " + Chambers.Count + "\nUsing: " + Chambers.Count(x => x.Value.GetName != emptyChamberName));

            ConsoleInterfaceSelection("Reset? (y/n)", '*');
            ConsoleKey inputKey = Console.ReadKey().Key;
            switch (inputKey)
            {
                case (ConsoleKey.Y):
                    {
                        string[] files = Directory.GetFiles(Directory.GetCurrentDirectory() + "\\Subjects").Select(Path.GetFileName).ToArray();
                        foreach (string fileName in files)
                            File.Delete(Directory.GetCurrentDirectory() + "\\Subjects\\" + fileName);

                        Chambers.Clear();
                        for (int i = 1; i <= MaxSubject; i++)
                            Chambers.Add(i, new Bot(emptyChamberName, i));

                        ConsoleCommandReport("All subjects removed", true);
                        break;
                    }
                case (ConsoleKey.N):
                    {
                        ConsoleCommandReport("Cancel", true);
                        break;
                    }
                default:
                    {
                        ConsoleCommandReport("Bad command", false);
                        break;
                    }
            }
            Console.ReadKey();
        }
        #endregion

        #region ConsoleInterface
        private void ConsoleReset()
        {
            Console.ForegroundColor = textColor;
            Console.BackgroundColor = textBg;
        }
        private void ConsoleCommandReport(string message, bool status, char symbol = '=')
        {
            int extraSpaces = 0;
            string statusMessage;
            if (status)
            {
                statusMessage = "OK: ";
                extraSpaces = statusMessage.Length;
                Console.Write(ConsoleSpaceDisplay(message.Length + extraSpaces, symbol));
                Console.ForegroundColor = okStatusColor;
                Console.Write(statusMessage);
            }
            else
            {
                statusMessage = "ERROR: ";
                extraSpaces = statusMessage.Length;
                Console.Write(ConsoleSpaceDisplay(message.Length + extraSpaces, symbol));
                Console.ForegroundColor = badStatusColor;
                Console.Write(statusMessage);
            }           

            ConsoleReset();
            Console.Write(message);

            Console.Write(ConsoleSpaceDisplay(message.Length + extraSpaces, symbol));
        }
        private void ConsoleStatusDisplay(bool status, string trueMessage, string falseMessage)
        {
            if(status)
            {
                Console.ForegroundColor = okStatusColor;
                Console.Write(trueMessage);
            }
            else
            {
                Console.ForegroundColor = badStatusColor;
                Console.Write(falseMessage);
            }
            ConsoleReset();
        }
        private void ConsoleInterfaceSelection(string message, char symbol)
        {
            Console.Write(ConsoleSpaceDisplay(message.Length, symbol));

            Console.BackgroundColor = sectionBgColor;
            Console.ForegroundColor = sectionTextColor;
            Console.Write(message);
            ConsoleReset();

            Console.Write(ConsoleSpaceDisplay(message.Length, symbol));
        }
        private string ConsoleSpaceDisplay(int length, char symbol)
        {
            string ConsoleSpace = "\n";
            for (int i = 0; i < length; i++)
                ConsoleSpace += symbol;
            return ConsoleSpace + '\n';
        }
        #endregion
    }
}
