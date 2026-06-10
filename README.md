import time
import tkinter as tk
from tkinter import messagebox
import pandas as pd


try:
    import winsound
    IS_WINDOWS = True
except ImportError:
    IS_WINDOWS = False


alarm_time_str = ""
alarm_active = False


def set_alarm():
    """Triggered when the user clicks the 'Set Alarm' button."""
    global alarm_time_str, alarm_active
    
    user_input = entry.get()
    
    try:
        
        alarm_time_str = pd.to_datetime(user_input).strftime("%I:%M %p")
        status_label.config(text=f"Alarm active for: {alarm_time_str}", fg="green")
        alarm_active = True
    except Exception:
    
        messagebox.showerror("Error", "Invalid time format! Use something like '08:30 AM' or '10:15 PM'")


def check_alarm():
    """The background function that checks the time every second."""
    global alarm_active
    
    current_time = pd.Timestamp.now().strftime("%I:%M %p")
    clock_label.config(text=current_time)  
    
    if alarm_active and current_time == alarm_time_str:
        alarm_active = False  
        status_label.config(text="Status: Alarm Triggered!", fg="red")
        
        
        trigger_audio_alert()
        
        
        messagebox.showinfo("Alarm", "Wake up! Time's up!")
        status_label.config(text="Status: No alarm set", fg="black")

    root.after(1000, check_alarm)


def trigger_audio_alert():
    """Handles playing the sound system-appropriately."""
    if IS_WINDOWS:
        for _ in range(5):
            winsound.Beep(1000, 600)
            time.sleep(0.3)
    else:
        for _ in range(5):
            print('\a')  
            time.sleep(0.5)


root = tk.Tk()
root.title("Pandas Alarm Clock")
root.geometry("350x250")
root.resizable(False, False)


clock_label = tk.Label(root, text="", font=("Helvetica", 32, "bold"))
clock_label.pack(pady=15)

instruction_label = tk.Label(root, text="Enter alarm time", font=("Helvetica", 10))
instruction_label.pack()

entry = tk.Entry(root, font=("Helvetica", 12), justify="center")
entry.pack(pady=5)


set_button = tk.Button(root, text="Set Alarm", command=set_alarm, font=("Helvetica", 10, "bold"), bg="#4CAF50", fg="white")
set_button.pack(pady=10)

status_label = tk.Label(root, text="Status: No alarm set", font=("Helvetica", 10, "italic"))
status_label.pack(pady=10)

check_alarm()

root.mainloop()
