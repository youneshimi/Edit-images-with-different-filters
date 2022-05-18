MATLAB App Designer GUI: Designing a program that can edit images with different filters

EDITOR Executable

1. Prerequisites for Deployment 

Verify that version 9.10 (R2021a) of the MATLAB Runtime is installed.   
If not, you can run the MATLAB Runtime installer.
To find its location, enter
  
    >>mcrinstaller
      
at the MATLAB prompt.
NOTE: You will need administrator rights to run the MATLAB Runtime installer. 

Alternatively, download and install the Windows version of the MATLAB Runtime for R2021a 
from the following link on the MathWorks website:

    https://www.mathworks.com/products/compiler/mcr/index.html
   
For more information about the MATLAB Runtime and the MATLAB Runtime installer, see 
"Distribute Applications" in the MATLAB Compiler documentation  
in the MathWorks Documentation Center.

2. Files to Deploy and Package

Files to Package for Standalone 
================================
-EDITOR.exe
-MCRInstaller.exe 
    Note: if end users are unable to download the MATLAB Runtime using the
    instructions in the previous section, include it when building your 
    component by clicking the "Runtime included in package" link in the
    Deployment Tool.
-This readme file 



3. Definitions

For information on deployment terminology, go to
https://www.mathworks.com/help and select MATLAB Compiler >
Getting Started > About Application Deployment >
Deployment Product Terms in the MathWorks Documentation
Center.




_______________________________________________________________________________________

classdef app1 < matlab.apps.AppBase

    % Properties that correspond to app components
    properties (Access = public)
        PhotoEditorUIFigure      matlab.ui.Figure
        ShowOriginalSwitch       matlab.ui.control.Switch
        ShowOriginalSwitchLabel  matlab.ui.control.Label
        ExportButton             matlab.ui.control.Button
        validateButton           matlab.ui.control.Button
        Slider_8                 matlab.ui.control.Slider
        Slider_7                 matlab.ui.control.Slider
        HighTripletLabel         matlab.ui.control.Label
        LowTripletLabel          matlab.ui.control.Label
        AdjustcontrastLabel      matlab.ui.control.Label
        Slider_6                 matlab.ui.control.Slider
        Slider_3                 matlab.ui.control.Slider
        Slider_2                 matlab.ui.control.Slider
        Slider                   matlab.ui.control.Slider
        NoiseSlider              matlab.ui.control.Slider
        NoiseSliderLabel         matlab.ui.control.Label
        BrowseButton             matlab.ui.control.Button
        imageEditField           matlab.ui.control.EditField
        imageEditFieldLabel      matlab.ui.control.Label
        PhotoEditorLabel         matlab.ui.control.Label
        UIAxes                   matlab.ui.control.UIAxes
    end

    
    properties (Access = private)
       Image
       Modified
       TempEffect
       Noise
       Exit 
    % Description
       LowR
       LowG
       LowB
       HighR
       HighG
       HighB
    end
    

    % Callbacks that handle component events
    methods (Access = private)

        % Code that executes after component creation
        function startupFcn(app)
            %Supprimer le titre, les étiquettes d'axe et les étiquettes de graduation
            title(app.UIAxes, []);
            xlabel(app.UIAxes, []);
            ylabel(app.UIAxes, []);
            app.UIAxes.XAxis.TickLabels = {};
            app.UIAxes.YAxis.TickLabels = {};
            %paramètres initialisés
            app.Exit = 0;
            app.LowR = 0.2;
            app.LowG = 0.3;
            app.LowB = 0;
            app.HighR = 0.6;
            app.HighG = 0.6;
            app.HighB = 1;
            
        end

        % Button pushed function: BrowseButton
        function BrowseButtonPushed(app, event)
            [filename,pathname] = uigetfile({'.jpg';'.png';'.bmp';'.gif';'.tif';'.*'},'File Selector');
            app.imageEditField.Value = strcat(pathname,filename);
            % Importation de l'image sélectionnée
            app.Image = imread(app.imageEditField.Value); 
            % set initial modified image 
            app.Modified = app.Image;
            % définir l'image modifiée initiale
            I = imshow(app.Modified, 'Parent', app.UIAxes, ...
                'XData', [1 app.UIAxes.Position(3)], ...
                'YData', [1 app.UIAxes.Position(4)]);
            % définir les limites des axes
            app.UIAxes.XLim = [0 I.XData(2)];
            app.UIAxes.YLim = [0 I.YData(2)];
            app.PhotoEditorUIFigure.Visible = 'off';
            app.PhotoEditorUIFigure.Visible = 'on';
                
                
        end

        % Value changed function: NoiseSlider
        function NoiseSliderValueChanged(app, event)
            app.Noise = app.NoiseSlider.Value;
            app.TempEffect = app.Modified;
            if app.Noise > 0 %0 is not accepted 
                app.TempEffect = imnoise(app.Modified,'gaussian',app.Noise);
                % Afficher l'image et l'étirer pour remplir les axes
                I = imshow(app.TempEffect, 'Parent',app.UIAxes, ...
                    'XData', [1 app.UIAxes.Position(3)], ...
                    'YData', [1 app.UIAxes.Position(4)]);
                % définir les limites des axes
            app.UIAxes.XLim = [0 I.XData(2)];
            app.UIAxes.YLim = [0 I.YData(2)];
            else
                app.TempEffect = app.Modified;
                % Afficher l'image et l'étirer pour remplir les axes
                 I = imshow(app.TempEffect, 'Parent',app.UIAxes, ...
                    'XData', [1 app.UIAxes.Position(3)], ...
                    'YData', [1 app.UIAxes.Position(4)]);
                % définir les limites des axes
            app.UIAxes.XLim = [0 I.XData(2)];
            app.UIAxes.YLim = [0 I.YData(2)];
            end
            app.Exit = 1;
            
        end

        % Value changed function: ShowOriginalSwitch
        function ShowOriginalSwitchValueChanged(app, event)
           if app.Exit == 1
               app.Modified = app.TempEffect;
               app.Exit =0; % reset
           end
           value = app.ShowOriginalSwitch.Value;
           if strcmp(value, 'On') == 1
               I = imshow(app.Image, 'Parent', app.UIAxes, ...
                    'XData', [1 app.UIAxes.Position(3)], ...
                    'YData', [1 app.UIAxes.Position(4)]);
                % définir les limites des axes
            app.UIAxes.XLim = [0 I.XData(2)];
            app.UIAxes.YLim = [0 I.YData(2)];
           else
               imshow(app.Modified, 'Parent', app.UIAxes, ...
                    'XData', [1 app.UIAxes.Position(3)], ...
                    'YData', [1 app.UIAxes.Position(4)]);
                % set the axes limits
                app.UIAxes.XLim = [0 I.XData(2)];
                app.UIAxes.YLim = [0 I.YData(2)];
           end
            
        end

        % Value changed function: Slider
        function SliderValueChanged(app, event)
            app.LowR = app.Slider.Value;
            app.TempEffect = imadjust(app.Modified, [app.LowR app.LowG app.LowB; app.HighR app.HighG app.HighB],[]);
            I = imshow(app.Image, 'Parent', app.UIAxes, ...
                    'XData', [1 app.UIAxes.Position(3)], ...
                    'YData', [1 app.UIAxes.Position(4)]);
                % définir les limites des axes
            app.UIAxes.XLim = [0 I.XData(2)];
            app.UIAxes.YLim = [0 I.YData(2)];
        end

        % Value changed function: Slider_2
        function Slider_2ValueChanged(app, event)
            app.LowG = app.Slider_2.Value;
            app.TempEffect = imadjust(app.Modified, [app.LowR app.LowG app.LowB; app.HighR app.HighG app.HighB],[]);
            I = imshow(app.Image, 'Parent', app.UIAxes, ...
                    'XData', [1 app.UIAxes.Position(3)], ...
                    'YData', [1 app.UIAxes.Position(4)]);
                % définir les limites des axes
            app.UIAxes.XLim = [0 I.XData(2)];
            app.UIAxes.YLim = [0 I.YData(2)];
        end

        % Value changed function: Slider_6
        function Slider_6ValueChanged(app, event)
            app.LowB = app.Slider_6.Value;
            app.TempEffect = imadjust(app.Modified, [app.LowR app.LowG app.LowB; app.HighR app.HighG app.HighB],[]);
            I = imshow(app.Image, 'Parent', app.UIAxes, ...
                    'XData', [1 app.UIAxes.Position(3)], ...
                    'YData', [1 app.UIAxes.Position(4)]);
                % définir les limites des axes
            app.UIAxes.XLim = [0 I.XData(2)];
            app.UIAxes.YLim = [0 I.YData(2)];
            
        end

        % Value changed function: Slider_7
        function Slider_7ValueChanged(app, event)
            app.HighR = app.Slider_7.Value;
            app.TempEffect = imadjust(app.Modified, [app.LowR app.LowG app.LowB; app.HighR app.HighG app.HighB],[]);
            I = imshow(app.Image, 'Parent', app.UIAxes, ...
                    'XData', [1 app.UIAxes.Position(3)], ...
                    'YData', [1 app.UIAxes.Position(4)]);
                % définir les limites des axes
            app.UIAxes.XLim = [0 I.XData(2)];
            app.UIAxes.YLim = [0 I.YData(2)];  
        end

        % Value changed function: Slider_8
        function Slider_8ValueChanged(app, event)
            app.HighG = app.Slider_8.Value;
            app.TempEffect = imadjust(app.Modified, [app.LowR app.LowG app.LowB; app.HighR app.HighG app.HighB],[]);
            I = imshow(app.Image, 'Parent', app.UIAxes, ...
                    'XData', [1 app.UIAxes.Position(3)], ...
                    'YData', [1 app.UIAxes.Position(4)]);
                % définir les limites des axes
            app.UIAxes.XLim = [0 I.XData(2)];
            app.UIAxes.YLim = [0 I.YData(2)];  
        end

        % Value changed function: Slider_3
        function Slider_3ValueChanged(app, event)
            app.HighB = app.Slider_3.Value;
            app.TempEffect = imadjust(app.Modified, [app.LowR app.LowG app.LowB; app.HighR app.HighG app.HighB],[]);
            I = imshow(app.Image, 'Parent', app.UIAxes, ...
                    'XData', [1 app.UIAxes.Position(3)], ...
                    'YData', [1 app.UIAxes.Position(4)]);
                %définir les limites des axes
            app.UIAxes.XLim = [0 I.XData(2)];
            app.UIAxes.YLim = [0 I.YData(2)];   
        end

        % Button pushed function: validateButton
        function validateButtonPushed(app, event)
          app.Modified = app.TempEffect;
          I = imshow(app.Modified, 'Parent', app.UIAxes, ...
                    'XData', [1 app.UIAxes.Position(3)], ...
                    'YData', [1 app.UIAxes.Position(4)]);
                % définir les limites des axes
            app.UIAxes.XLim = [0 I.XData(2)];
            app.UIAxes.YLim = [0 I.YData(2)];
        end

        % Button pushed function: ExportButton
        function ExportButtonPushed(app, event)
            filter = {'.jpg';'.png';'.bmp';'.gif';'.tif';'.*'};
            [file, path] = uiputfile(filter);
            if file~=0
                fig = figure;
                fig.Visible = 'off';
                figAxes = axes(fig);
                allChildren = app.UIAxes.XLim;
                copyobj(allChildren, figAxes)
                figAxes.XLim = app.UIAxes.XLim;
                figAxes.YLim = app.UIAxes.YLim;
                figAxes.DataAspectRatio = app.UIAxes.DataAspectRatio;
                % Supprimer les étiquettes de coche
                figAxes.XAxis.TickLabels = {};
                figAxes.YAxis.TickLabels = {};
                % Supprimer l'axe
                set(gca, 'Visible', 'off');
                % enregistrer en tant que fichier .xyz
                saveas(fig, strcat(path,file));
                % Supprimer la figure temporaire
                delete(fig);
            end
        end
    end

    % Component initialization
    methods (Access = private)

        % Create UIFigure and components
        function createComponents(app)

            % Create PhotoEditorUIFigure and hide until all components are created
            app.PhotoEditorUIFigure = uifigure('Visible', 'off');
            app.PhotoEditorUIFigure.Position = [100 100 920 637];
            app.PhotoEditorUIFigure.Name = 'Photo Editor';
            app.PhotoEditorUIFigure.Icon = 'Photo Editor.png';

            % Create UIAxes
            app.UIAxes = uiaxes(app.PhotoEditorUIFigure);
            title(app.UIAxes, 'Title')
            xlabel(app.UIAxes, 'X')
            ylabel(app.UIAxes, 'Y')
            zlabel(app.UIAxes, 'Z')
            app.UIAxes.ColorOrder = [0 0.4471 0.7412;0.851 0.3255 0.098;0.9294 0.6941 0.1255;0.4941 0.1843 0.5569;0.4667 0.6745 0.1882;0.302 0.7451 0.9333;0.6392 0.0784 0.1804];
            app.UIAxes.Position = [487 173 433 360];

            % Create PhotoEditorLabel
            app.PhotoEditorLabel = uilabel(app.PhotoEditorUIFigure);
            app.PhotoEditorLabel.FontName = 'Yu Gothic Medium';
            app.PhotoEditorLabel.FontSize = 18;
            app.PhotoEditorLabel.FontWeight = 'bold';
            app.PhotoEditorLabel.Position = [58 571 109 31];
            app.PhotoEditorLabel.Text = 'Photo Editor';

            % Create imageEditFieldLabel
            app.imageEditFieldLabel = uilabel(app.PhotoEditorUIFigure);
            app.imageEditFieldLabel.HorizontalAlignment = 'right';
            app.imageEditFieldLabel.FontSize = 14;
            app.imageEditFieldLabel.Position = [19 506 44 22];
            app.imageEditFieldLabel.Text = 'image';

            % Create imageEditField
            app.imageEditField = uieditfield(app.PhotoEditorUIFigure, 'text');
            app.imageEditField.Position = [78 506 320 22];

            % Create BrowseButton
            app.BrowseButton = uibutton(app.PhotoEditorUIFigure, 'push');
            app.BrowseButton.ButtonPushedFcn = createCallbackFcn(app, @BrowseButtonPushed, true);
            app.BrowseButton.Icon = 'loupe.png';
            app.BrowseButton.Position = [401 503 91 29];
            app.BrowseButton.Text = 'Browse';

            % Create NoiseSliderLabel
            app.NoiseSliderLabel = uilabel(app.PhotoEditorUIFigure);
            app.NoiseSliderLabel.HorizontalAlignment = 'right';
            app.NoiseSliderLabel.FontSize = 14;
            app.NoiseSliderLabel.FontWeight = 'bold';
            app.NoiseSliderLabel.Position = [34 443 44 22];
            app.NoiseSliderLabel.Text = 'Noise';

            % Create NoiseSlider
            app.NoiseSlider = uislider(app.PhotoEditorUIFigure);
            app.NoiseSlider.Limits = [0 1];
            app.NoiseSlider.ValueChangedFcn = createCallbackFcn(app, @NoiseSliderValueChanged, true);
            app.NoiseSlider.MinorTicks = 1;
            app.NoiseSlider.Position = [99 452 374 3];

            % Create Slider
            app.Slider = uislider(app.PhotoEditorUIFigure);
            app.Slider.Limits = [0 0.5];
            app.Slider.Orientation = 'vertical';
            app.Slider.ValueChangedFcn = createCallbackFcn(app, @SliderValueChanged, true);
            app.Slider.Position = [12 74 3 150];
            app.Slider.Value = 0.3;

            % Create Slider_2
            app.Slider_2 = uislider(app.PhotoEditorUIFigure);
            app.Slider_2.Limits = [0 0.5];
            app.Slider_2.Orientation = 'vertical';
            app.Slider_2.ValueChangedFcn = createCallbackFcn(app, @Slider_2ValueChanged, true);
            app.Slider_2.Position = [87 74 3 150];
            app.Slider_2.Value = 0.1;

            % Create Slider_3
            app.Slider_3 = uislider(app.PhotoEditorUIFigure);
            app.Slider_3.Limits = [0.5 1];
            app.Slider_3.Orientation = 'vertical';
            app.Slider_3.ValueChangedFcn = createCallbackFcn(app, @Slider_3ValueChanged, true);
            app.Slider_3.Position = [450 71 3 150];
            app.Slider_3.Value = 1;

            % Create Slider_6
            app.Slider_6 = uislider(app.PhotoEditorUIFigure);
            app.Slider_6.Limits = [0 0.5];
            app.Slider_6.Orientation = 'vertical';
            app.Slider_6.ValueChangedFcn = createCallbackFcn(app, @Slider_6ValueChanged, true);
            app.Slider_6.Position = [167 71 3 150];
            app.Slider_6.Value = 0.4;

            % Create AdjustcontrastLabel
            app.AdjustcontrastLabel = uilabel(app.PhotoEditorUIFigure);
            app.AdjustcontrastLabel.HorizontalAlignment = 'center';
            app.AdjustcontrastLabel.FontWeight = 'bold';
            app.AdjustcontrastLabel.Position = [175 242 168 33];
            app.AdjustcontrastLabel.Text = 'Adjust contrast';

            % Create LowTripletLabel
            app.LowTripletLabel = uilabel(app.PhotoEditorUIFigure);
            app.LowTripletLabel.HorizontalAlignment = 'center';
            app.LowTripletLabel.Position = [43 36 92 22];
            app.LowTripletLabel.Text = 'Low Triplet';

            % Create HighTripletLabel
            app.HighTripletLabel = uilabel(app.PhotoEditorUIFigure);
            app.HighTripletLabel.HorizontalAlignment = 'center';
            app.HighTripletLabel.Position = [338 36 92 22];
            app.HighTripletLabel.Text = 'High Triplet';

            % Create Slider_7
            app.Slider_7 = uislider(app.PhotoEditorUIFigure);
            app.Slider_7.Limits = [0.5 1];
            app.Slider_7.Orientation = 'vertical';
            app.Slider_7.ValueChangedFcn = createCallbackFcn(app, @Slider_7ValueChanged, true);
            app.Slider_7.Position = [288 72 3 150];
            app.Slider_7.Value = 0.6;

            % Create Slider_8
            app.Slider_8 = uislider(app.PhotoEditorUIFigure);
            app.Slider_8.Limits = [0.5 1];
            app.Slider_8.Orientation = 'vertical';
            app.Slider_8.ValueChangedFcn = createCallbackFcn(app, @Slider_8ValueChanged, true);
            app.Slider_8.Position = [366 72 3 150];
            app.Slider_8.Value = 0.8;

            % Create validateButton
            app.validateButton = uibutton(app.PhotoEditorUIFigure, 'push');
            app.validateButton.ButtonPushedFcn = createCallbackFcn(app, @validateButtonPushed, true);
            app.validateButton.Icon = 'accept.png';
            app.validateButton.FontSize = 18;
            app.validateButton.FontWeight = 'bold';
            app.validateButton.Position = [179 324 177 41];
            app.validateButton.Text = 'validate';

            % Create ExportButton
            app.ExportButton = uibutton(app.PhotoEditorUIFigure, 'push');
            app.ExportButton.ButtonPushedFcn = createCallbackFcn(app, @ExportButtonPushed, true);
            app.ExportButton.Icon = 'export.png';
            app.ExportButton.FontSize = 18;
            app.ExportButton.FontWeight = 'bold';
            app.ExportButton.Position = [546 83 177 41];
            app.ExportButton.Text = 'Export';

            % Create ShowOriginalSwitchLabel
            app.ShowOriginalSwitchLabel = uilabel(app.PhotoEditorUIFigure);
            app.ShowOriginalSwitchLabel.HorizontalAlignment = 'center';
            app.ShowOriginalSwitchLabel.FontWeight = 'bold';
            app.ShowOriginalSwitchLabel.Position = [801 81 86 22];
            app.ShowOriginalSwitchLabel.Text = 'Show Original';

            % Create ShowOriginalSwitch
            app.ShowOriginalSwitch = uiswitch(app.PhotoEditorUIFigure, 'slider');
            app.ShowOriginalSwitch.ValueChangedFcn = createCallbackFcn(app, @ShowOriginalSwitchValueChanged, true);
            app.ShowOriginalSwitch.Position = [820 118 45 20];

            % Show the figure after all components are created
            app.PhotoEditorUIFigure.Visible = 'on';
        end
    end

    % App creation and deletion
    methods (Access = public)

        % Construct app
        function app = app1

            % Create UIFigure and components
            createComponents(app)

            % Register the app with App Designer
            registerApp(app, app.PhotoEditorUIFigure)

            % Execute the startup function
            runStartupFcn(app, @startupFcn)

            if nargout == 0
                clear app
            end
        end

        % Code that executes before app deletion
        function delete(app)

            % Delete UIFigure when app is deleted
            delete(app.PhotoEditorUIFigure)
        end
    end
end


