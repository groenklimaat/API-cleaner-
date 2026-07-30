#!/usr/bin/env python3
"""
╔═══════════════════════════════════════════════════════════════════════════════╗
║        🧺 OFFLINE WASMACHINE PRO - With DELETE function!                   ║
║           Fast washing · Clean up desktop · Delete · Everything in order   ║
╚═══════════════════════════════════════════════════════════════════════════════╝
"""

import os
import shutil
import re
import json
import threading
import time
from pathlib import Path
from datetime import datetime
from collections import Counter
import tkinter as tk
from tkinter import *
from tkinter import ttk, filedialog, messagebox, scrolledtext

# ============================================================================
# WASHING MACHINE CONFIGURATION
# ============================================================================

class WasmachineSettings:
    """Settings for the offline washing machine"""
    
    API_JUNK = {
        '.json', '.yaml', '.yml', '.xml', '.proto',
        '.graphql', '.gql', '.raml', '.swagger',
        '.log', '.out', '.error', '.trace',
        '.cache', '.tmp', '.temp',
        '.dump', '.sql', '.sqlite', '.db',
        '.env', '.config', '.conf', '.ini',
    }
    
    API_KEYWORDS = {
        'api', 'rest', 'swagger', 'openapi', 'graphql',
        'endpoint', 'request', 'response', 'json',
        'auth', 'token', 'key', 'secret', 'password',
        'dump', 'cache', 'temp', 'tmp',
        'log', 'error', 'debug', 'trace',
        'schema', 'validation', 'spec',
        'oauth', 'jwt', 'bearer', 'basic_auth'
    }
    
    API_FOLDERS = {
        'api', 'rest', 'swagger', 'graphql', 'endpoints',
        'apis', 'rest_api', 'api_docs', 'api_spec'
    }
    
    SKIP_FOLDERS = {
        'Windows', 'Program Files', 'Program Files (x86)',
        'System32', 'System', '$Recycle.Bin',
        'System Volume Information', 'temp', 'tmp',
        'cache', 'logs', 'AppData', 'Microsoft',
        '.git', 'node_modules', 'venv', '__pycache__'
    }


class OfflineWasmachine:
    """The Offline API Washing Machine with Delete function"""
    
    def __init__(self):
        self.settings = WasmachineSettings()
        self.is_running = False
        self.progress = 0
        self.current_file = ""
        self.total_files = 0
        self.processed_files = 0
        self.start_time = None
        self.end_time = None
        
        self.stats = {
            'total_washed': 0,
            'api_junk_removed': 0,
            'clean_files': 0,
            'total_size_mb': 0,
            'saved_mb': 0,
            'api_files': [],
            'clean_files_list': [],
            'deleted': [],
            'errors': []
        }
        
        # For desktop cleanup
        self.desktop_path = Path.home() / "Desktop"
        self.downloads_path = Path.home() / "Downloads"
        self.documents_path = Path.home() / "Documents"
    
    def is_api_junk(self, filepath: Path) -> tuple:
        """Check if file is API junk"""
        name = filepath.name.lower()
        path = str(filepath).lower()
        extension = filepath.suffix.lower()
        
        for folder in self.settings.API_FOLDERS:
            if f'\\{folder}\\' in path or f'/{folder}/' in path:
                return True, f"API_folder_{folder}"
        
        if extension in self.settings.API_JUNK:
            return True, "API_extension"
        
        for keyword in self.settings.API_KEYWORDS:
            if keyword in name:
                return True, f"API_keyword_{keyword}"
            if keyword in path:
                return True, f"API_keyword_in_path_{keyword}"
        
        return False, "clean"
    
    def get_category(self, filepath: Path) -> str:
        """Determine category for clean files"""
        ext = filepath.suffix.lower()
        name = filepath.name.lower()
        
        if ext in {'.doc', '.docx', '.pdf', '.txt', '.rtf', '.odt'}:
            return "📄 Documents"
        elif ext in {'.xls', '.xlsx', '.ods', '.csv'}:
            return "📊 Spreadsheets"
        elif ext in {'.ppt', '.pptx', '.odp'}:
            return "📽️ Presentations"
        elif ext in {'.jpg', '.jpeg', '.png', '.gif', '.bmp', '.tiff'}:
            return "🖼️ Images"
        elif ext in {'.svg', '.ico', '.webp', '.psd', '.ai'}:
            return "🎨 Design"
        elif ext in {'.mp4', '.avi', '.mkv', '.mov', '.wmv', '.flv'}:
            return "🎬 Video"
        elif ext in {'.mp3', '.wav', '.flac', '.aac', '.ogg'}:
            return "🎵 Audio"
        elif ext in {'.zip', '.rar', '.7z', '.tar', '.gz', '.bz2'}:
            return "📦 Archives"
        elif ext in {'.py', '.pyw', '.js', '.html', '.css', '.php', '.cpp', '.java'}:
            return "💻 Code"
        elif ext in {'.exe', '.msi', '.app', '.deb'}:
            return "⚙️ Programs"
        elif 'backup' in name:
            return "💾 Backups"
        elif 'project' in name:
            return "📁 Projects"
        else:
            return "📁 Other"
    
    def wash_file(self, filepath: Path, destination: Path, delete: bool = False) -> bool:
        """Wash a file - Delete or move"""
        try:
            if not filepath.exists():
                return False
            
            size_bytes = os.path.getsize(str(filepath))
            size_mb = size_bytes / (1024 * 1024)
            
            self.stats['total_washed'] += 1
            self.stats['total_size_mb'] += size_mb
            
            is_junk, reason = self.is_api_junk(filepath)
            
            if is_junk:
                if delete:
                    # 🔥 DELETE - Gone for good!
                    os.remove(str(filepath))
                    self.stats['api_junk_removed'] += 1
                    self.stats['saved_mb'] += size_mb
                    self.stats['deleted'].append({
                        'name': filepath.name,
                        'path': str(filepath),
                        'reason': reason,
                        'size_mb': round(size_mb, 2)
                    })
                    return True
                else:
                    # Move to API_JUNK folder
                    junk_dest = destination / "API_JUNK" / datetime.now().strftime("%Y-%m-%d")
                    junk_dest.mkdir(parents=True, exist_ok=True)
                    
                    timestamp = datetime.now().strftime("%H%M%S")
                    new_name = f"{filepath.stem}_{timestamp}{filepath.suffix}"
                    dest_path = junk_dest / new_name
                    
                    shutil.move(str(filepath), str(dest_path))
                    
                    self.stats['api_junk_removed'] += 1
                    self.stats['saved_mb'] += size_mb
                    self.stats['api_files'].append({
                        'name': filepath.name,
                        'path': str(filepath),
                        'reason': reason,
                        'size_mb': round(size_mb, 2)
                    })
                    return True
            else:
                # Clean file
                if delete:
                    # 🔥 Also delete clean files if chosen
                    os.remove(str(filepath))
                    self.stats['deleted'].append({
                        'name': filepath.name,
                        'path': str(filepath),
                        'reason': "Manually deleted",
                        'size_mb': round(size_mb, 2)
                    })
                    return True
                else:
                    # Keep with category
                    category = self.get_category(filepath)
                    clean_dest = destination / "CLEAN_WASH" / category
                    clean_dest.mkdir(parents=True, exist_ok=True)
                    
                    shutil.move(str(filepath), str(clean_dest / filepath.name))
                    
                    self.stats['clean_files'] += 1
                    self.stats['clean_files_list'].append({
                        'name': filepath.name,
                        'path': str(filepath),
                        'category': category,
                        'size_mb': round(size_mb, 2)
                    })
                    return True
                
        except Exception as e:
            self.stats['errors'].append(f"{filepath.name}: {str(e)}")
            return False
    
    def wash_folder(self, source_folder: Path, destination: Path, delete: bool = False, callback=None):
        """Wash all files in a folder"""
        self.is_running = True
        self.start_time = datetime.now()
        self.processed_files = 0
        
        # Reset stats
        self.stats = {
            'total_washed': 0,
            'api_junk_removed': 0,
            'clean_files': 0,
            'total_size_mb': 0,
            'saved_mb': 0,
            'api_files': [],
            'clean_files_list': [],
            'deleted': [],
            'errors': []
        }
        
        # Count files
        self.total_files = 0
        for root, dirs, files in os.walk(source_folder):
            if any(skip in str(root) for skip in self.settings.SKIP_FOLDERS):
                continue
            self.total_files += len(files)
        
        # Wash files
        for root, dirs, files in os.walk(source_folder):
            if not self.is_running:
                break
            
            if any(skip in str(root) for skip in self.settings.SKIP_FOLDERS):
                continue
            
            for file in files:
                if not self.is_running:
                    break
                
                filepath = Path(root) / file
                self.current_file = str(filepath)
                self.wash_file(filepath, destination, delete)
                
                self.processed_files += 1
                if callback and self.processed_files % 10 == 0:
                    progress = (self.processed_files / self.total_files * 100) if self.total_files > 0 else 0
                    callback(progress, self.processed_files, self.total_files, self.current_file)
        
        self.end_time = datetime.now()
        self.is_running = False
        
        if callback:
            callback(100, self.processed_files, self.total_files, "✅ Done!")
    
    def quick_wash(self, destination: Path, delete: bool = False, callback=None):
        """Quick wash - Desktop + Downloads + Documents"""
        self.is_running = True
        self.start_time = datetime.now()
        
        # Reset stats
        self.stats = {
            'total_washed': 0,
            'api_junk_removed': 0,
            'clean_files': 0,
            'total_size_mb': 0,
            'saved_mb': 0,
            'api_files': [],
            'clean_files_list': [],
            'deleted': [],
            'errors': []
        }
        
        folders = [
            (self.desktop_path, "Desktop"),
            (self.downloads_path, "Downloads"),
            (self.documents_path, "Documents")
        ]
        
        total_files = 0
        for folder_path, name in folders:
            if folder_path.exists():
                for root, dirs, files in os.walk(folder_path):
                    total_files += len(files)
        
        self.total_files = total_files
        self.processed_files = 0
        
        for folder_path, name in folders:
            if not folder_path.exists():
                continue
            
            if callback:
                callback(0, self.processed_files, self.total_files, f"📂 Washing {name}...")
            
            for root, dirs, files in os.walk(folder_path):
                if not self.is_running:
                    break
                
                for file in files:
                    if not self.is_running:
                        break
                    
                    filepath = Path(root) / file
                    self.current_file = str(filepath)
                    self.wash_file(filepath, destination, delete)
                    
                    self.processed_files += 1
                    if callback and self.processed_files % 5 == 0:
                        progress = (self.processed_files / self.total_files * 100) if self.total_files > 0 else 0
                        callback(progress, self.processed_files, self.total_files, f"🧺 {filepath.name}")
        
        self.end_time = datetime.now()
        self.is_running = False
        
        if callback:
            callback(100, self.processed_files, self.total_files, "✅ Quick wash complete!")
    
    def get_stats(self) -> dict:
        """Get statistics"""
        duration = 0
        if self.start_time and self.end_time:
            duration = (self.end_time - self.start_time).total_seconds()
            if duration < 0:
                duration = 0
        
        return {
            'total_washed': self.stats['total_washed'],
            'api_junk_removed': self.stats['api_junk_removed'],
            'clean_files': self.stats['clean_files'],
            'total_size_mb': round(self.stats['total_size_mb'], 2),
            'total_size_gb': round(self.stats['total_size_mb'] / 1024, 2),
            'saved_mb': round(self.stats['saved_mb'], 2),
            'saved_gb': round(self.stats['saved_mb'] / 1024, 2),
            'api_files': self.stats['api_files'][:100],
            'clean_files_list': self.stats['clean_files_list'][:100],
            'deleted': self.stats['deleted'][:100],
            'duration': round(duration, 2),
            'is_running': self.is_running,
            'api_percentage': round((self.stats['api_junk_removed'] / self.stats['total_washed'] * 100), 2) if self.stats['total_washed'] > 0 else 0,
            'errors': self.stats['errors'][:20]
        }


# ============================================================================
# GUI
# ============================================================================

class WasmachineApp:
    def __init__(self, root):
        self.root = root
        self.root.title("🧺 Offline Wasmachine Pro - With Delete!")
        self.root.geometry("1100x850")
        self.root.resizable(True, True)
        
        self.wasmachine = OfflineWasmachine()
        self.scan_thread = None
        self.delete_mode = False  # Default: don't delete
        
        self._setup_ui()
        
    def _setup_ui(self):
        main = ttk.Frame(self.root, padding="10")
        main.grid(row=0, column=0, sticky=(N,W,E,S))
        self.root.columnconfigure(0, weight=1)
        self.root.rowconfigure(0, weight=1)
        main.columnconfigure(0, weight=1)
        main.rowconfigure(3, weight=1)
        
        # Title
        title = ttk.Frame(main)
        title.grid(row=0, column=0, columnspan=2, pady=(0,10))
        ttk.Label(title, text="🧺 OFFLINE WASHING MACHINE PRO", font=("Arial", 18, "bold")).pack()
        ttk.Label(title, text="❤️🐳🐳🐳❤️ With DELETE function! · Quick wash · Everything in order!", font=("Arial", 10)).pack()
        
        # Configuration
        cfg = ttk.LabelFrame(main, text="📁 Configuration", padding="10")
        cfg.grid(row=1, column=0, columnspan=2, sticky=(W,E), pady=5)
        
        # Source folder
        ttk.Label(cfg, text="📂 Source folder:").grid(row=0, column=0, sticky=W, pady=2)
        self.source_entry = ttk.Entry(cfg, width=60)
        self.source_entry.grid(row=0, column=1, padx=5, sticky=(W,E))
        self.source_entry.insert(0, str(Path.home() / "Desktop"))
        ttk.Button(cfg, text="Browse", command=self._select_source).grid(row=0, column=2, padx=5)
        
        # Destination folder
        ttk.Label(cfg, text="📀 Destination folder:").grid(row=1, column=0, sticky=W, pady=5)
        self.dest_entry = ttk.Entry(cfg, width=60)
        self.dest_entry.grid(row=1, column=1, padx=5, sticky=(W,E))
        self.dest_entry.insert(0, "C:\\Wasmachine_Output")
        ttk.Button(cfg, text="Browse", command=self._select_destination).grid(row=1, column=2, padx=5)
        
        # Options
        options_frame = ttk.Frame(cfg)
        options_frame.grid(row=2, column=0, columnspan=3, pady=10)
        
        self.delete_var = BooleanVar(value=False)
        ttk.Checkbutton(options_frame, text="🔥 DELETE (INSTEAD OF MOVE)", 
                       variable=self.delete_var).pack(side=LEFT, padx=10)
        
        # Buttons
        btn_frame = ttk.Frame(cfg)
        btn_frame.grid(row=3, column=0, columnspan=3, pady=5)
        
        self.quick_btn = ttk.Button(btn_frame, text="⚡ Quick Wash", command=self._quick_wash)
        self.quick_btn.grid(row=0, column=0, padx=5)
        
        self.wash_btn = ttk.Button(btn_frame, text="🧺 Start Wash", command=self._start_wash)
        self.wash_btn.grid(row=0, column=1, padx=5)
        
        self.stop_btn = ttk.Button(btn_frame, text="⏹ Stop", command=self._stop_wash, state=DISABLED)
        self.stop_btn.grid(row=0, column=2, padx=5)
        
        self.stats_btn = ttk.Button(btn_frame, text="📊 Statistics", command=self._show_stats)
        self.stats_btn.grid(row=0, column=3, padx=5)
        
        self.clear_btn = ttk.Button(btn_frame, text="🗑️ Clear Log", command=self._clear_log)
        self.clear_btn.grid(row=0, column=4, padx=5)
        
        # Progress
        progress_frame = ttk.Frame(cfg)
        progress_frame.grid(row=4, column=0, columnspan=3, sticky=(W,E), pady=5)
        
        self.progress_var = DoubleVar()
        self.progress_bar = ttk.Progressbar(progress_frame, variable=self.progress_var, maximum=100)
        self.progress_bar.pack(fill=X, pady=5)
        
        self.status_label = ttk.Label(progress_frame, text="⚪ Ready to wash")
        self.status_label.pack()
        
        # Log
        log_frame = ttk.LabelFrame(main, text="📋 Log", padding="5")
        log_frame.grid(row=2, column=0, columnspan=2, sticky=(W,E), pady=5)
        
        self.log_text = scrolledtext.ScrolledText(log_frame, height=8, font=("Courier", 9))
        self.log_text.pack(fill=BOTH, expand=True)
        
        # Results
        result_frame = ttk.LabelFrame(main, text="📊 Results", padding="5")
        result_frame.grid(row=3, column=0, columnspan=2, sticky=(N,W,E,S), pady=5)
        result_frame.columnconfigure(0, weight=1)
        result_frame.columnconfigure(1, weight=1)
        result_frame.columnconfigure(2, weight=1)
        result_frame.rowconfigure(0, weight=1)
        
        # API Junk
        api_frame = ttk.LabelFrame(result_frame, text="🗑️ API Junk (Washed away)", padding="5")
        api_frame.grid(row=0, column=0, sticky=(N,W,E,S), padx=2)
        self.api_listbox = Listbox(api_frame, height=8)
        self.api_listbox.pack(fill=BOTH, expand=True)
        
        # Clean Wash
        clean_frame = ttk.LabelFrame(result_frame, text="✅ Clean Wash (Preserved)", padding="5")
        clean_frame.grid(row=0, column=1, sticky=(N,W,E,S), padx=2)
        self.clean_listbox = Listbox(clean_frame, height=8)
        self.clean_listbox.pack(fill=BOTH, expand=True)
        
        # Deleted
        delete_frame = ttk.LabelFrame(result_frame, text="🔥 Deleted (Permanently gone!)", padding="5")
        delete_frame.grid(row=0, column=2, sticky=(N,W,E,S), padx=2)
        self.delete_listbox = Listbox(delete_frame, height=8)
        self.delete_listbox.pack(fill=BOTH, expand=True)
        
        # Status bar
        self.status_bar = ttk.Label(main, text="🧺 Offline Mode - Delete mode: OFF", relief=SUNKEN, anchor=W)
        self.status_bar.grid(row=4, column=0, columnspan=2, sticky=(W,E), pady=(5,0))
        
        # Color tags for log
        self.log_text.tag_configure("success", foreground="green")
        self.log_text.tag_configure("error", foreground="red")
        self.log_text.tag_configure("info", foreground="blue")
        self.log_text.tag_configure("warning", foreground="orange")
        self.log_text.tag_configure("delete", foreground="red", font=("Courier", 9, "bold"))
    
    def _log(self, msg, tag=None):
        timestamp = datetime.now().strftime("%H:%M:%S")
        formatted = f"[{timestamp}] {msg}\n"
        if tag:
            self.log_text.insert(END, formatted, tag)
        else:
            self.log_text.insert(END, formatted)
        self.log_text.see(END)
        self.root.update_idletasks()
    
    def _select_source(self):
        folder = filedialog.askdirectory(title="Select source folder")
        if folder:
            self.source_entry.delete(0, END)
            self.source_entry.insert(0, folder)
    
    def _select_destination(self):
        folder = filedialog.askdirectory(title="Select destination folder")
        if folder:
            self.dest_entry.delete(0, END)
            self.dest_entry.insert(0, folder)
    
    def _update_progress(self, progress, current, total, current_file):
        self.progress_var.set(progress)
        self.status_label.config(text=f"📂 {current_file} ({current}/{total})")
        self.root.update_idletasks()
    
    def _quick_wash(self):
        """Quick wash - Desktop + Downloads + Documents"""
        destination = self.dest_entry.get().strip()
        delete = self.delete_var.get()
        
        if not destination:
            messagebox.showerror("Error", "Select a destination folder!")
            return
        
        if self.wasmachine.is_running:
            messagebox.showwarning("Warning", "Washing machine is already running!")
            return
        
        if delete:
            answer = messagebox.askyesno("⚠️ WARNING", 
                "🔥 DELETE MODE IS ON!\n\n"
                "Files will be PERMANENTLY DELETED!\n"
                "This CANNOT be undone!\n\n"
                "Are you sure you want to continue?")
            if not answer:
                return
        
        self._log("⚡ START QUICK WASH!", "info")
        self._log(f"📂 Destination: {destination}", "info")
        self._log(f"🔥 Delete mode: {'ON' if delete else 'OFF'}", "delete" if delete else "info")
        self._log("🧺 Washing Desktop, Downloads and Documents...", "info")
        
        self.quick_btn.config(state=DISABLED)
        self.wash_btn.config(state=DISABLED)
        self.stop_btn.config(state=NORMAL)
        self.status_bar.config(text=f"🧺 Running... Delete mode: {'ON' if delete else 'OFF'}")
        
        def wash_thread():
            self.wasmachine.quick_wash(Path(destination), delete, self._update_progress)
            self.root.after(0, self._wash_complete)
        
        self.scan_thread = threading.Thread(target=wash_thread, daemon=True)
        self.scan_thread.start()
    
    def _start_wash(self):
        """Start wash with selected folder"""
        source = self.source_entry.get().strip()
        destination = self.dest_entry.get().strip()
        delete = self.delete_var.get()
        
        if not source:
            messagebox.showerror("Error", "Select a source folder!")
            return
        if not destination:
            messagebox.showerror("Error", "Select a destination folder!")
            return
        
        if self.wasmachine.is_running:
            messagebox.showwarning("Warning", "Washing machine is already running!")
            return
        
        if delete:
            answer = messagebox.askyesno("⚠️ WARNING", 
                "🔥 DELETE MODE IS ON!\n\n"
                "Files will be PERMANENTLY DELETED!\n"
                "This CANNOT be undone!\n\n"
                "Are you sure you want to continue?")
            if not answer:
                return
        
        self._log(f"🧺 START WASH: {source}", "info")
        self._log(f"📂 Destination: {destination}", "info")
        self._log(f"🔥 Delete mode: {'ON' if delete else 'OFF'}", "delete" if delete else "info")
        
        self.quick_btn.config(state=DISABLED)
        self.wash_btn.config(state=DISABLED)
        self.stop_btn.config(state=NORMAL)
        self.status_bar.config(text=f"🧺 Running... Delete mode: {'ON' if delete else 'OFF'}")
        
        def wash_thread():
            self.wasmachine.wash_folder(Path(source), Path(destination), delete, self._update_progress)
            self.root.after(0, self._wash_complete)
        
        self.scan_thread = threading.Thread(target=wash_thread, daemon=True)
        self.scan_thread.start()
    
    def _stop_wash(self):
        """Stop washing"""
        if self.wasmachine.is_running:
            self.wasmachine.is_running = False
            self._log("⏹ Wash stopped!", "warning")
            self.status_label.config(text="⏹ Stopped")
            self.quick_btn.config(state=NORMAL)
            self.wash_btn.config(state=NORMAL)
            self.stop_btn.config(state=DISABLED)
            self.status_bar.config(text="🧺 Stopped")
    
    def _wash_complete(self):
        """Wash complete"""
        self.quick_btn.config(state=NORMAL)
        self.wash_btn.config(state=NORMAL)
        self.stop_btn.config(state=DISABLED)
        
        stats = self.wasmachine.get_stats()
        
        self._log("="*60, "info")
        self._log("✅ WASH COMPLETE!", "success")
        self._log(f"📊 Total washed: {stats['total_washed']}", "info")
        self._log(f"🗑️ API junk removed: {stats['api_junk_removed']}", "error")
        self._log(f"✅ Clean files: {stats['clean_files']}", "success")
        self._log(f"🔥 Deleted: {len(stats['deleted'])}", "delete")
        self._log(f"💾 Saved: {stats['saved_gb']} GB", "info")
        self._log(f"⏱️ Time: {stats['duration']} seconds", "info")
        self._log("="*60, "info")
        
        self.status_label.config(text="✅ Wash complete!")
        self.progress_var.set(100)
        self.status_bar.config(text=f"✅ Done! {stats['api_junk_removed']} API files removed!")
        
        # Update result lists
        self._update_results()
        
        msg = f"✅ Wash complete!\n\n"
        msg += f"🗑️ API junk removed: {stats['api_junk_removed']}\n"
        msg += f"✅ Clean files: {stats['clean_files']}\n"
        msg += f"🔥 Deleted: {len(stats['deleted'])}\n"
        msg += f"💾 Saved: {stats['saved_gb']} GB\n"
        msg += f"⏱️ Time: {stats['duration']} seconds"
        
        if stats['errors']:
            msg += f"\n\n⚠️ Errors: {len(stats['errors'])}"
        
        messagebox.showinfo("Wash Complete", msg)
    
    def _update_results(self):
        """Update result lists"""
        stats = self.wasmachine.get_stats()
        
        # API junk
        self.api_listbox.delete(0, END)
        for file in stats['api_files'][:50]:
            self.api_listbox.insert(END, f"🗑️ {file['name']} ({file['size_mb']} MB)")
        
        # Clean wash
        self.clean_listbox.delete(0, END)
        for file in stats['clean_files_list'][:50]:
            self.clean_listbox.insert(END, f"✅ {file['name']} ({file['size_mb']} MB)")
        
        # Deleted
        self.delete_listbox.delete(0, END)
        for file in stats['deleted'][:50]:
            self.delete_listbox.insert(END, f"🔥 {file['name']} ({file['size_mb']} MB)")
    
    def _show_stats(self):
        """Show statistics"""
        stats = self.wasmachine.get_stats()
        
        msg = f"""
📊 WASHING MACHINE STATISTICS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  🧺 Total washed:       {stats['total_washed']}
  🗑️ API junk removed:   {stats['api_junk_removed']}
  ✅ Clean files:         {stats['clean_files']}
  🔥 Deleted:             {len(stats['deleted'])}
  📊 API percentage:      {stats['api_percentage']}%
  
  💾 Total size:          {stats['total_size_gb']} GB
  💾 Saved:               {stats['saved_gb']} GB
  
  ⏱️ Time:                {stats['duration']} seconds
  ❌ Errors:              {len(stats['errors'])}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
        """
        
        messagebox.showinfo("Statistics", msg)
    
    def _clear_log(self):
        """Clear log"""
        self.log_text.delete(1.0, END)
        self._log("🗑️ Log cleared", "info")


# ============================================================================
# START
# ============================================================================

def main():
    root = Tk()
    app = WasmachineApp(root)
    
    def on_closing():
        if app.wasmachine.is_running:
            app.wasmachine.is_running = False
        root.destroy()
    
    root.protocol("WM_DELETE_WINDOW", on_closing)
    root.mainloop()


if __name__ == "__main__":
    print("="*70)
    print("🧺 OFFLINE WASHING MACHINE PRO - With DELETE function!")
    print("="*70)
    print("❤️🐳🐳🐳❤️ No psutil required!")
    print("⚡ Quick wash · Clean up desktop · DELETE · Everything in order")
    print("="*70)
    print("🔥 Delete mode: ENABLE with checkbox!")
    print("⚠️ WARNING: Deleted files are PERMANENTLY gone!")
    print("="*70)
    main()
