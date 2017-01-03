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
    class Subject
    {
        #region Constants
        const int MaxStat = 100,
          MinStat = 15,
          DeadlyStat = 0,
          StepHour = 6,
          DeductHp = 10,
          DeductHunger = 10,
          DeductSleep = 15,
          AddSleep = 10;
        // const int pauseTime = 1000;
        const string SavesPath = @"Subjects\";
        #endregion

        string _name;
        int _hp,
            _hunger,
            _sleep,
            _lifeDays,
            _daysGone;
        bool _isSleeping;
        bool _isAlive;

        public Subject(string name, int id)
        {
            _hp = MaxStat;
            _hunger = MaxStat;
            _sleep = MaxStat;
            _name = name;
            _lifeDays = 0;
            _daysGone = 0;
            _isSleeping = false;
            _isAlive = true;
            ID = id;
        }
        public void Step()
        {
            if (_daysGone % 10 == 0)
            {
                _lifeDays++;
                Save();
            }
            _daysGone++;

            #region HP
            if (hp == DeadlyStat)
            {
                _isAlive = false;
                Dead();
                return;
            }
            else if (hp < MaxStat && hunger > DeadlyStat && sleep > DeadlyStat)
            {
                hp++;
                if (IsSleeping)
                    hp++;
            }
            else if (hunger == DeadlyStat)
            {
                hp -= DeductHp;
                IsSleeping = false;
            }
            if (sleep == DeadlyStat)
                hp -= DeductHp;
            #endregion
            #region HUNGER
            if (hunger > DeadlyStat)
                hunger -= DeductHunger;
            if (hunger <= MinStat && FeedTime != null)
                FeedTime();
            #endregion
            #region SLEEP
            if (sleep <= MinStat && SleepTime != null)
                SleepTime();
            if (IsSleeping)
                if (sleep == MaxStat)
                    IsSleeping = false;
                else
                    sleep += AddSleep;
            if (sleep > DeadlyStat && !IsSleeping)
                sleep -= DeductSleep;
            #endregion

            Thread.Sleep(APTC.TerminalMenu.pauseTime);
        }
        public void Display()
        {
            Console.WriteLine("Day: {0}\nHP: {1,-3}\nHunger: {2,-3}\nSleep: {3,-3}", _lifeDays, _hp, _hunger, _sleep);
        }
        public void Save()
        {
            Subject saveSubject = this;
            using (Stream output = File.Create(SavesPath + Convert.ToString(ID)))
            {
                BinaryFormatter formatter = new BinaryFormatter();
                formatter.Serialize(output, saveSubject);
            }
        }
        public void Load()
        {
            using (Stream loadFile = File.OpenRead(SavesPath + Convert.ToString(this.ID)))
            {
                BinaryFormatter formatter = new BinaryFormatter();
                Subject loadSubject = (Subject)formatter.Deserialize(loadFile);

                this._name = loadSubject._name;
                this._hp = loadSubject._hp;
                this._hunger = loadSubject._hunger;
                this._sleep = loadSubject._sleep;
                this._lifeDays = loadSubject._lifeDays;
                this._daysGone = loadSubject._daysGone;
                this._isSleeping = loadSubject._isSleeping;
                this._isAlive = loadSubject._isAlive;
            }
        }

        #region Свойства
        //private
        private int hp { get { return _hp; }
            set { if (value <= 0)
                    _hp = 0;
                else
                    _hp = value; } }
        private int hunger { get { return _hunger; }
            set { if (value <= 0)
                    _hunger = 0;
                else
                    _hunger = value; } }
        private int sleep { get { return _sleep; }
            set { if (value <= 0)
                    _sleep = 0;
                else
                    _sleep = value; } }
        //public
        public int Hunger { get { return hunger; }
            set { if ((hunger + value) >= MaxStat)
                    hunger = MaxStat;
                else
                    hunger = value; } }
        public int ID { get; set; }
        public bool IsSleeping { private get { return _isSleeping; }
            set { if (value == false)
                    WakeUp();
                _isSleeping = value; } }
        public bool IsAlive { get { return _isAlive; } }
        public string Name { get { return _name; } }
        public Action GetDisplay { get { return Display; } }
        #endregion

        #region События
        public event Action FeedTime;
        public event Action SleepTime;
        public event Action Dead;
        public event Action WakeUp;
        #endregion
    }
} 
