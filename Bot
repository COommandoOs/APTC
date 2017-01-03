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
using System.Runtime.Serialization.Formatters.Binary;

namespace APTC
{
    [Serializable]
    public class Bot
    {        
        Subject subject;
        Random rand;
        /*======================*/
        [NonSerialized]
        private Thread thread;
        /*======================*/
        public Bot(string name, int id)
        {
            subject = new Subject(name, id);
            rand = new Random();
            thread = new Thread(Cycle);
            thread.IsBackground = true;

            subject.FeedTime += Subject_FeedTime;
            subject.SleepTime += Subject_SleepTime;
            subject.Dead += Subject_Dead;
            subject.WakeUp += Subject_WakeUp;
        }

        public void DisplayStat()
        {
            subject.GetDisplay();
        }
        public void Cycle()
        {
            while (subject.IsAlive)
            {
                subject.Step();
            }
        }
        private void Subject_FeedTime()
        {
            int fullness = rand.Next(10, 90);
           // PostMessage(ConsoleColor.Yellow, "\n\n\t" + subject.Name + " что-то таки и съел(+" + fullness + ")");
            subject.Hunger += fullness;
        }
        private void Subject_SleepTime()
        {
          //  PostMessage(ConsoleColor.Cyan, "\n\n\t" + subject.Name + " прилёг поспать.");
            subject.IsSleeping = true;
        }
        private void Subject_Dead()
        {
            Save();
            thread.Suspend();
            thread.Abort();
            //    PostMessage(ConsoleColor.Red, "\n\n\t" + subject.Name + " умер!");
        }
        private void Subject_WakeUp()
        {
       //     PostMessage(ConsoleColor.Green, "\n\n\t" + subject.Name + " проснулся.");
        }

        public void Save()
        {
            subject.Save();
        }
        public void Load()
        {
            subject.Load();
        }        

        public void ExecuteCommand(Action method)
        {
            method();
        }
        private void ActivateChamber()
        {
            if(GetLifeStatus)
                if (thread.IsAlive)
                    thread.Resume();
                else
                    thread.Start();
        }
        private void DeactivateChamber()
        {
            if(GetLifeStatus)
                thread.Suspend();
        }
        private void KillSubject()
        {
            subject.FeedTime -= Subject_FeedTime;
            subject.SleepTime -= Subject_SleepTime;
        }

        public bool GetLifeStatus { get { return subject.IsAlive; } }
        public string GetName { get { return subject.Name; } }
        public Action GetActivateChamber { get { return ActivateChamber; } }
        public Action GetDeactivateChamber { get { return DeactivateChamber; } }
        public Action GetKillSubject { get { return KillSubject; } }
        public Action GetSave { get { return Save; } }
        public bool GetThreadStatus { get
            {
                string state = thread.ThreadState.ToString();
                if (state.Contains("Suspended") || state.Contains("Unstarted"))
                    return false;
                else
                    return true;
            } }
    }
}
