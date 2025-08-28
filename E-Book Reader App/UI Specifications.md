# User Interface Specifications - E-Book Reader App

## 🎨 Design System

### Color Palette
```css
:root {
  /* Day Theme */
  --day-bg: #FFFFFF;
  --day-text: #000000;
  --day-accent: #3498db;
  --day-surface: #F8F9FA;
  --day-border: #E9ECEF;
  
  /* Night Theme */
  --night-bg: #1A1A1A;
  --night-text: #E0E0E0;
  --night-accent: #4CAF50;
  --night-surface: #2D2D2D;
  --night-border: #404040;
  
  /* Sepia Theme */
  --sepia-bg: #F4F1E8;
  --sepia-text: #5D4E37;
  --sepia-accent: #8B4513;
  --sepia-surface: #F0E68C;
  --sepia-border: #DDD;
  
  /* High Contrast Theme */
  --contrast-bg: #000000;
  --contrast-text: #FFFFFF;
  --contrast-accent: #FFFF00;
  --contrast-surface: #333333;
  --contrast-border: #FFFFFF;
  
  /* Highlight Colors */
  --highlight-yellow: #FFEB3B;
  --highlight-green: #4CAF50;
  --highlight-blue: #2196F3;
  --highlight-pink: #E91E63;
  --highlight-orange: #FF9800;
}
```

### Typography Scale
```css
.typography-scale {
  --font-xs: 12px;
  --font-sm: 14px;
  --font-base: 16px;
  --font-lg: 18px;
  --font-xl: 20px;
  --font-2xl: 24px;
  --font-3xl: 30px;
  --font-4xl: 36px;
  
  --line-height-tight: 1.2;
  --line-height-normal: 1.5;
  --line-height-relaxed: 1.8;
  --line-height-loose: 2.0;
  
  --letter-spacing-tight: -0.5px;
  --letter-spacing-normal: 0px;
  --letter-spacing-wide: 0.5px;
  --letter-spacing-wider: 1px;
}
```

### Spacing System
```css
.spacing-system {
  --space-xs: 4px;
  --space-sm: 8px;
  --space-md: 16px;
  --space-lg: 24px;
  --space-xl: 32px;
  --space-2xl: 48px;
  --space-3xl: 64px;
  
  --margin-reading: 20px;
  --margin-comfortable: 32px;
  --margin-wide: 48px;
}
```

## 📚 Library Interface

### Bookshelf Layout
```jsx
// Component structure for bookshelf view
const BookshelfView = () => {
  return (
    <ScrollView style={styles.library}>
      {/* Header with search and filters */}
      <LibraryHeader />
      
      {/* Currently Reading Section */}
      <Section title="Leyendo Ahora">
        <HorizontalBookList books={currentlyReading} />
      </Section>
      
      {/* Recent Books */}
      <Section title="Recientes">
        <BookGrid books={recentBooks} columns={3} />
      </Section>
      
      {/* Favorites */}
      <Section title="Favoritos">
        <BookGrid books={favoriteBooks} columns={4} />
      </Section>
      
      {/* By Author */}
      <Section title="Por Autor">
        <AuthorCarousel authors={authors} />
      </Section>
      
      {/* By Tags */}
      <Section title="Etiquetas">
        <TagChips tags={tags} />
      </Section>
    </ScrollView>
  );
};
```

### Book Cover Display
```jsx
const BookCover = ({ book, size = 'medium' }) => {
  const coverStyles = {
    small: { width: 80, height: 120 },
    medium: { width: 120, height: 180 },
    large: { width: 160, height: 240 }
  };
  
  return (
    <View style={[styles.bookCover, coverStyles[size]]}>
      {/* Cover Image */}
      <Image 
        source={{ uri: book.coverImage }} 
        style={styles.coverImage}
        defaultSource={require('../assets/default-cover.png')}
      />
      
      {/* Progress Indicator */}
      <ProgressRing 
        progress={book.readingProgress} 
        size={24}
        style={styles.progressRing}
      />
      
      {/* Book Info Overlay */}
      <View style={styles.bookInfo}>
        <Text style={styles.title} numberOfLines={2}>
          {book.title}
        </Text>
        <Text style={styles.author} numberOfLines={1}>
          {book.author}
        </Text>
      </View>
      
      {/* Favorite Star */}
      {book.isFavorite && (
        <Icon name="star" style={styles.favoriteIcon} />
      )}
    </View>
  );
};
```

## 📖 Reading Interface

### Reading Screen Layout
```jsx
const ReadingScreen = () => {
  return (
    <View style={styles.readingContainer}>
      {/* Status Bar (hideable) */}
      <StatusBar 
        currentPage={currentPage}
        totalPages={totalPages}
        battery={batteryLevel}
        time={currentTime}
        style={statusBarVisible ? styles.visible : styles.hidden}
      />
      
      {/* Reading Content */}
      <View style={styles.contentArea}>
        <BookContent 
          content={currentPageContent}
          settings={readingSettings}
          onPageChange={handlePageChange}
        />
        
        {/* Reading Ruler (if enabled) */}
        {rulerEnabled && (
          <ReadingRuler 
            position={rulerPosition}
            style={rulerStyle}
          />
        )}
      </View>
      
      {/* Control Overlay (hideable) */}
      <ReadingControls 
        visible={controlsVisible}
        onSettingsPress={showSettings}
        onBookmarkPress={addBookmark}
        onSearchPress={showSearch}
      />
      
      {/* Gesture Areas */}
      <GestureOverlay onGesture={handleGesture} />
    </View>
  );
};
```

### Page Content Rendering
```jsx
const BookContent = ({ content, settings, theme }) => {
  const contentStyle = {
    fontSize: settings.fontSize,
    lineHeight: settings.lineHeight,
    fontFamily: settings.fontFamily,
    textAlign: settings.textAlign,
    color: theme.text,
    backgroundColor: theme.background,
    paddingHorizontal: settings.marginHorizontal,
    paddingVertical: settings.marginVertical,
    letterSpacing: settings.letterSpacing
  };
  
  return (
    <ScrollView 
      style={styles.contentScroll}
      contentContainerStyle={contentStyle}
      scrollEnabled={settings.scrollMode === 'continuous'}
    >
      {/* Rendered HTML content with highlights and notes */}
      <RenderHTML 
        content={content}
        highlights={highlights}
        notes={notes}
        onTextSelect={handleTextSelection}
        onHighlightPress={showHighlightMenu}
      />
    </ScrollView>
  );
};
```

## ⚙️ Settings Interface

### Reading Settings Panel
```jsx
const ReadingSettings = () => {
  return (
    <Modal visible={settingsVisible} animationType="slide">
      <View style={styles.settingsContainer}>
        <Header title="Configuración de Lectura" />
        
        <ScrollView style={styles.settingsScroll}>
          {/* Theme Selection */}
          <SettingsSection title="Tema">
            <ThemeSelector 
              themes={availableThemes}
              selected={currentTheme}
              onSelect={setTheme}
            />
          </SettingsSection>
          
          {/* Typography Settings */}
          <SettingsSection title="Tipografía">
            <FontSelector 
              fonts={availableFonts}
              selected={currentFont}
              onSelect={setFont}
            />
            <Slider 
              label="Tamaño de Fuente"
              min={8}
              max={72}
              value={fontSize}
              onChange={setFontSize}
            />
            <Slider 
              label="Interlineado"
              min={0.8}
              max={3.0}
              step={0.1}
              value={lineHeight}
              onChange={setLineHeight}
            />
            <Slider 
              label="Espaciado de Letras"
              min={-2}
              max={5}
              step={0.5}
              value={letterSpacing}
              onChange={setLetterSpacing}
            />
          </SettingsSection>
          
          {/* Layout Settings */}
          <SettingsSection title="Diseño">
            <SegmentedControl 
              options={['Izquierda', 'Centro', 'Derecha', 'Justificado']}
              selected={textAlign}
              onChange={setTextAlign}
            />
            <MarginAdjuster 
              margins={margins}
              onChange={setMargins}
            />
          </SettingsSection>
          
          {/* Page Animation */}
          <SettingsSection title="Animaciones">
            <AnimationSelector 
              animations={pageAnimations}
              selected={currentAnimation}
              onSelect={setPageAnimation}
            />
          </SettingsSection>
          
          {/* Reading Aids */}
          <SettingsSection title="Ayudas de Lectura">
            <Switch 
              label="Regla de Lectura"
              value={rulerEnabled}
              onChange={setRulerEnabled}
            />
            <Switch 
              label="Auto-scroll"
              value={autoScrollEnabled}
              onChange={setAutoScrollEnabled}
            />
            {autoScrollEnabled && (
              <Slider 
                label="Velocidad de Auto-scroll"
                min={1}
                max={10}
                value={autoScrollSpeed}
                onChange={setAutoScrollSpeed}
              />
            )}
          </SettingsSection>
        </ScrollView>
      </View>
    </Modal>
  );
};
```

## 🎮 Gesture Configuration

### Gesture Settings Interface
```jsx
const GestureSettings = () => {
  return (
    <View style={styles.gestureSettings}>
      <Header title="Configuración de Gestos" />
      
      {/* Tap Zones */}
      <SettingsSection title="Zonas de Toque">
        <TapZoneVisualizer 
          zones={tapZones}
          onZonePress={editTapZone}
        />
        <ActionSelector 
          zone={selectedZone}
          actions={availableActions}
          onSelect={assignAction}
        />
      </SettingsSection>
      
      {/* Swipe Gestures */}
      <SettingsSection title="Gestos de Deslizamiento">
        <GestureList 
          gestures={swipeGestures}
          onEdit={editGesture}
        />
      </SettingsSection>
      
      {/* Hardware Controls */}
      <SettingsSection title="Controles de Hardware">
        <Switch 
          label="Botones de Volumen para Páginas"
          value={volumeButtonsEnabled}
          onChange={setVolumeButtonsEnabled}
        />
        <Switch 
          label="Teclado Físico"
          value={keyboardEnabled}
          onChange={setKeyboardEnabled}
        />
      </SettingsSection>
      
      {/* Gesture Sensitivity */}
      <SettingsSection title="Sensibilidad">
        <Slider 
          label="Umbral de Deslizamiento"
          min={20}
          max={100}
          value={swipeThreshold}
          onChange={setSwipeThreshold}
        />
        <Slider 
          label="Tiempo de Pulsación Larga"
          min={200}
          max={1000}
          value={longPressThreshold}
          onChange={setLongPressThreshold}
        />
      </SettingsSection>
    </View>
  );
};
```

## 📝 Text Tools Interface

### Highlighting and Notes
```jsx
const TextSelectionMenu = ({ selection, position }) => {
  return (
    <View style={[styles.selectionMenu, { top: position.y, left: position.x }]}>
      {/* Highlight Options */}
      <View style={styles.highlightColors}>
        {highlightColors.map(color => (
          <TouchableOpacity 
            key={color}
            style={[styles.colorButton, { backgroundColor: color }]}
            onPress={() => highlightText(selection, color)}
          />
        ))}
      </View>
      
      {/* Action Buttons */}
      <View style={styles.actionButtons}>
        <Button 
          title="Nota" 
          onPress={() => addNote(selection)}
          icon="note"
        />
        <Button 
          title="Copiar" 
          onPress={() => copyText(selection)}
          icon="copy"
        />
        <Button 
          title="Buscar" 
          onPress={() => searchText(selection)}
          icon="search"
        />
        <Button 
          title="Traducir" 
          onPress={() => translateText(selection)}
          icon="translate"
        />
        <Button 
          title="Guardar" 
          onPress={() => saveQuote(selection)}
          icon="bookmark"
        />
      </View>
    </Modal>
  );
};
```

### Notes and Bookmarks Panel
```jsx
const NotesPanel = ({ bookId }) => {
  return (
    <Modal visible={notesPanelVisible} animationType="slide">
      <View style={styles.notesPanel}>
        <Header title="Notas y Marcadores" />
        
        <TabView>
          {/* Bookmarks Tab */}
          <Tab title="Marcadores">
            <BookmarkList 
              bookmarks={bookmarks}
              onBookmarkPress={goToBookmark}
              onBookmarkDelete={deleteBookmark}
            />
          </Tab>
          
          {/* Highlights Tab */}
          <Tab title="Resaltados">
            <HighlightList 
              highlights={highlights}
              onHighlightPress={goToHighlight}
              onHighlightEdit={editHighlight}
            />
          </Tab>
          
          {/* Notes Tab */}
          <Tab title="Notas">
            <NoteList 
              notes={notes}
              onNotePress={editNote}
              onNoteDelete={deleteNote}
            />
          </Tab>
          
          {/* Saved Quotes Tab */}
          <Tab title="Citas Guardadas">
            <QuoteList 
              quotes={savedQuotes}
              onQuoteShare={shareQuote}
              onQuoteExport={exportQuote}
            />
          </Tab>
        </TabView>
      </View>
    </Modal>
  );
};
```

## 🔍 Search Interface

### Search Screen
```jsx
const SearchScreen = ({ bookId }) => {
  return (
    <View style={styles.searchContainer}>
      <Header title="Buscar" />
      
      {/* Search Input */}
      <SearchInput 
        placeholder="Buscar en el libro..."
        value={searchQuery}
        onChange={setSearchQuery}
        onSubmit={performSearch}
      />
      
      {/* Search Options */}
      <SearchOptions>
        <Switch 
          label="Coincidir Mayúsculas"
          value={caseSensitive}
          onChange={setCaseSensitive}
        />
        <Switch 
          label="Palabra Completa"
          value={wholeWord}
          onChange={setWholeWord}
        />
        <Switch 
          label="Expresión Regular"
          value={useRegex}
          onChange={setUseRegex}
        />
      </SearchOptions>
      
      {/* Search Results */}
      <SearchResults 
        results={searchResults}
        onResultPress={goToResult}
        query={searchQuery}
      />
    </View>
  );
};
```

## 📊 Statistics Interface

### Reading Statistics Dashboard
```jsx
const StatisticsScreen = () => {
  return (
    <ScrollView style={styles.statsContainer}>
      <Header title="Estadísticas de Lectura" />
      
      {/* Overview Cards */}
      <View style={styles.overviewCards}>
        <StatCard 
          title="Tiempo de Lectura Hoy"
          value={todayReadingTime}
          icon="clock"
        />
        <StatCard 
          title="Páginas Leídas"
          value={pagesReadToday}
          icon="book"
        />
        <StatCard 
          title="Racha de Lectura"
          value={readingStreak}
          icon="fire"
        />
        <StatCard 
          title="Libros Completados"
          value={booksCompleted}
          icon="check"
        />
      </View>
      
      {/* Reading Time Chart */}
      <ChartSection title="Tiempo de Lectura">
        <LineChart 
          data={readingTimeData}
          period={selectedPeriod}
          onPeriodChange={setSelectedPeriod}
        />
      </ChartSection>
      
      {/* Reading Speed */}
      <ChartSection title="Velocidad de Lectura">
        <BarChart 
          data={readingSpeedData}
          yAxisLabel="Palabras por Minuto"
        />
      </ChartSection>
      
      {/* Book Progress */}
      <Section title="Progreso de Libros">
        <BookProgressList 
          books={booksInProgress}
          onBookPress={openBook}
        />
      </Section>
      
      {/* Reading Insights */}
      <Section title="Insights de Lectura">
        <InsightCards insights={readingInsights} />
      </Section>
    </ScrollView>
  );
};
```

## 🎨 Responsive Design Breakpoints

### Device Adaptation
```css
/* Phone (Portrait) */
@media (max-width: 480px) {
  .library-grid {
    grid-template-columns: repeat(2, 1fr);
  }
  
  .book-cover {
    width: 100px;
    height: 150px;
  }
  
  .reading-margins {
    padding: 16px;
  }
}

/* Phone (Landscape) */
@media (min-width: 481px) and (max-width: 768px) and (orientation: landscape) {
  .reading-content {
    columns: 2;
    column-gap: 32px;
  }
}

/* Tablet (Portrait) */
@media (min-width: 769px) and (max-width: 1024px) and (orientation: portrait) {
  .library-grid {
    grid-template-columns: repeat(4, 1fr);
  }
  
  .reading-margins {
    padding: 48px;
  }
}

/* Tablet (Landscape) */
@media (min-width: 1025px) and (orientation: landscape) {
  .library-grid {
    grid-template-columns: repeat(6, 1fr);
  }
  
  .reading-content {
    columns: 2;
    column-gap: 64px;
    max-width: 1200px;
    margin: 0 auto;
  }
}

/* E-Reader Screens */
@media (max-device-width: 600px) and (max-device-height: 800px) {
  .high-contrast-mode {
    filter: contrast(150%);
  }
  
  .e-ink-optimized {
    transition: none;
    animation: none;
  }
}
```

This UI specification provides comprehensive guidance for creating an intuitive, accessible, and highly customizable e-book reader interface that adapts to different devices and user preferences while maintaining the Spanish language focus.